# Agentic AI & MCP Server — How They Supercharge the Plugin

## 1. The Core Idea

Traditional ad management plugins are **forms + APIs** — humans fill forms, click buttons, read dashboards.

Agentic AI + MCP turns your plugin into an **AI-native system** where:
- An AI agent can *operate* the plugin (not just advise)
- A human can say "pause all underperforming campaigns" and it happens
- The plugin becomes a **tool** that any LLM (Claude, GPT-4o, Gemini) can call

This is a massive competitive moat — no legacy CMP (Celtra, Bannerflow, Sizmek) is AI-native yet.

---

## 2. What Is MCP (Model Context Protocol)?

MCP is an **open standard by Anthropic** for connecting AI models to external tools, data sources, and services.

```
Without MCP:                        With MCP:
──────────────────────────────────────────────────────────────
Human → UI → Plugin API             AI Agent → MCP Server → Plugin API
                                              ↕
                                    (AI understands what
                                     the plugin can do,
                                     calls it like a tool,
                                     returns structured results)
```

**An MCP server is a thin adapter** that:
1. Declares what **tools** are available (name, description, parameters)
2. Handles the AI's **tool call** → executes it against your plugin's API
3. Returns structured results the AI can reason about

Your plugin exposes an MCP server → Claude / any AI assistant becomes a UI.

---

## 3. MCP Server for the Plugin — Concrete Design

### Tools You'd Expose

```typescript
// MCP Tool Definitions (what the AI "sees")

Tool: search_creatives
  Description: Search the creative asset library
  Parameters:
    query: string          // "Nike banner Q4"
    status: enum           // draft | approved | rejected
    platform: string       // google_display | meta | dv360
    format: string         // html5 | mp4 | jpg

Tool: get_asset_spec_validation
  Description: Validate a creative against platform specs
  Parameters:
    asset_id: string
    platform: string

Tool: submit_creative_for_approval
  Description: Submit a creative asset into the approval workflow
  Parameters:
    asset_id: string
    workflow_id: string
    note: string            // optional reviewer note

Tool: get_campaign_performance
  Description: Retrieve campaign metrics from all connected platforms
  Parameters:
    campaign_id: string
    date_range: { start: string, end: string }
    metrics: string[]       // ["impressions","ctr","spend","cpa"]
    breakdown: enum         // platform | creative | ad_set | day

Tool: create_campaign
  Description: Create a new campaign structure in the plugin
  Parameters:
    name: string
    objective: enum         // awareness | consideration | conversion
    budget: number
    budget_type: enum       // daily | lifetime
    start_date: string
    end_date: string
    platform_targets: string[]

Tool: update_campaign_status
  Description: Pause, resume, or end a campaign
  Parameters:
    campaign_id: string
    action: enum            // pause | resume | end

Tool: list_campaigns
  Description: List campaigns with filters
  Parameters:
    status: enum            // active | paused | draft | ended
    platform: string
    date_range: object

Tool: assign_creative_to_campaign
  Description: Attach an approved creative to a campaign's ad set
  Parameters:
    campaign_id: string
    ad_set_id: string
    asset_id: string

Tool: get_creative_performance
  Description: Get performance breakdown at the creative level
  Parameters:
    asset_id: string
    date_range: object

Tool: detect_creative_fatigue
  Description: Identify creatives with declining performance
  Parameters:
    campaign_id: string
    ctr_drop_threshold: number   // e.g. 0.30 = 30% drop triggers flag
```

### MCP Server Structure (Node.js / TypeScript)

```typescript
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { PluginApiClient } from "./plugin-api-client.js";

const server = new Server(
  { name: "adcmp-plugin", version: "1.0.0" },
  { capabilities: { tools: {} } }
);

const api = new PluginApiClient({ baseUrl: process.env.PLUGIN_API_URL, apiKey: process.env.API_KEY });

server.setRequestHandler(ListToolsRequestSchema, async () => ({
  tools: [
    {
      name: "get_campaign_performance",
      description: "Retrieve performance metrics for a campaign across all connected platforms",
      inputSchema: {
        type: "object",
        properties: {
          campaign_id: { type: "string" },
          date_range: {
            type: "object",
            properties: {
              start: { type: "string", description: "ISO date YYYY-MM-DD" },
              end:   { type: "string", description: "ISO date YYYY-MM-DD" }
            }
          },
          metrics: { type: "array", items: { type: "string" } }
        },
        required: ["campaign_id"]
      }
    },
    // ... other tools
  ]
}));

server.setRequestHandler(CallToolRequestSchema, async (request) => {
  const { name, arguments: args } = request.params;

  switch (name) {
    case "get_campaign_performance":
      const data = await api.campaigns.getPerformance(args);
      return { content: [{ type: "text", text: JSON.stringify(data, null, 2) }] };

    case "update_campaign_status":
      await api.campaigns.updateStatus(args.campaign_id, args.action);
      return { content: [{ type: "text", text: `Campaign ${args.campaign_id} ${args.action}d successfully.` }] };

    case "detect_creative_fatigue":
      const fatigued = await api.creatives.detectFatigue(args);
      return { content: [{ type: "text", text: JSON.stringify(fatigued, null, 2) }] };

    // ... other cases
  }
});

const transport = new StdioServerTransport();
await server.connect(transport);
```

---

## 4. Agentic AI Use Cases in the Plugin

These are **autonomous workflows** the AI runs — not just answering questions, but taking action.

### Agent 1: Campaign Performance Monitor

```
Trigger: Daily at 8am (scheduled) or on-demand

Agent behavior:
1. Calls get_campaign_performance for all active campaigns
2. Identifies campaigns where CPA > target by > 20%
3. Identifies campaigns where budget is 80% spent with 40%+ time remaining
4. For each flagged campaign:
   → Calls update_campaign_status (pause) if CPA is 2× target
   → Generates natural language summary: "Campaign X has a CPA of $45 vs $20 target. Paused pending review."
5. Posts Slack/email notification to campaign manager with findings + actions taken

Human-in-loop option: Instead of auto-pausing, generates a "triage report" for human approval
```

### Agent 2: Creative Spec Fixer

```
Trigger: On asset upload (or on-demand for existing assets)

Agent behavior:
1. Calls get_asset_spec_validation for uploaded file
2. If issues found, analyzes each failure:
   → File too large: "This HTML5 file is 163KB, Google Display limit is 150KB.
      Suggestion: remove font embed, use web-safe font — saves ~15KB"
   → Missing click tag: "HTML5 file has no clickTag variable.
      Add: var clickTag = '' at the top of your JS"
3. Returns actionable fix instructions in plain language
4. If auto-fixable (image compression), optionally fixes and creates new version
```

### Agent 3: Campaign Brief-to-Structure

```
Trigger: User provides a campaign brief (text)

Input:
"Q4 Black Friday campaign for Nike Running. Budget $50k, runs Nov 20 - Dec 2.
Target: male runners 25-44. Channels: Google Display and Meta. Goal: online sales."

Agent behavior:
1. Parses brief → extracts: budget, dates, audience, channels, objective
2. Calls create_campaign with structured fields
3. Creates 2 ad sets (Google, Meta) with appropriate targeting defaults
4. Searches asset library: calls search_creatives for "Nike" + "display" + "Q4"
5. Assigns approved creatives to ad sets
6. Returns: "Campaign structure created. Found 3 approved creatives in library.
   Missing: 320×50 mobile banner for Meta. Flagged for production."
```

### Agent 4: Creative Refresh Advisor

```
Trigger: Weekly, or when fatigue detected

Agent behavior:
1. Calls detect_creative_fatigue for all active campaigns
2. For fatigued creatives:
   → Calls get_creative_performance to see full historical trend
   → Calls search_creatives to find related high-performing variants
3. Generates report:
   "Creative nike_rf_300x250_v1 CTR dropped 42% over last 14 days (freq: 6.2).
   Recommended replacements from library: nike_rf_300x250_v3 (CTR +18% in Campaign B)
   Action: Swap creative? [Yes / Schedule for Monday / Ignore]"
```

### Agent 5: Approval Workflow Assistant

```
Trigger: Reviewer opens an asset for review

Agent behavior:
1. Fetches creative metadata, spec validation results, brief, campaign context
2. Pre-fills review form with:
   → Spec status: "Passes Google Display. Fails Meta (file size 163KB > 150KB)"
   → Brand check: "Logo usage matches brand guidelines (version 2024-Q3)"
   → Legal flag: "Detected claim: 'Up to 40% off' — requires legal sign-off per policy"
3. Suggests action: "Recommend: Conditional Approve pending Meta spec fix"
```

---

## 5. MCP as a Distribution Channel

MCP turns your plugin into a **toolset any AI assistant can use**. This means:

```
User in Claude.ai:
"How are my Nike campaigns performing this week? Pause anything with CPA over $30."

Claude:
→ Calls get_campaign_performance (all Nike campaigns, last 7 days)
→ Identifies Campaign B: CPA = $38
→ Calls update_campaign_status (campaign_b, pause)
→ Responds: "Nike RunFast campaign has a CPA of $38 (target: $30). I've paused it.
   Your other 2 Nike campaigns are on track (CPA: $22, $18)."
```

This is a **chat interface to your entire plugin** — for free, using Claude/ChatGPT as the UI.

**Who benefits:**
| User | Without MCP | With MCP |
|------|------------|----------|
| Campaign Manager | Logs into plugin, navigates dashboards | Asks Claude in Slack/Teams, done |
| Analyst | Exports CSV, builds pivot table | "Compare creative performance Q3 vs Q4 for all video ads" |
| Creative Producer | Manually checks specs | "Is my new banner ready to traffic on Meta?" |
| Executive | Waits for weekly report | "What's our total ROAS this week vs last month?" |

---

## 6. Architecture: Plugin + MCP + Agents

```
┌─────────────────────────────────────────────────────────────┐
│                    AI LAYER                                 │
│  Claude / GPT-4o / Gemini                                   │
│  (conversational interface or automated agent)              │
└──────────────────────────┬──────────────────────────────────┘
                           │ MCP Protocol (tool calls)
┌──────────────────────────▼──────────────────────────────────┐
│                    MCP SERVER                               │
│  adcmp-mcp-server (Node.js)                                 │
│  Translates AI tool calls → Plugin REST API calls           │
│  Translates API responses → AI-readable text/JSON           │
└──────────────────────────┬──────────────────────────────────┘
                           │ REST API
┌──────────────────────────▼──────────────────────────────────┐
│                    PLUGIN CORE (your product)               │
│  ├── Campaign Service                                       │
│  ├── Creative Asset Service                                 │
│  ├── Approval Workflow Service                              │
│  ├── Spec Validation Service                                │
│  ├── Platform Connectors (Google, Meta, DV360...)           │
│  └── Analytics Aggregation                                 │
└──────────────────────────┬──────────────────────────────────┘
                           │ Platform APIs
                    Google / Meta / DV360 / TTD
```

**Key point:** The MCP server is thin. Your plugin's REST API is the real product. MCP is just an adapter.

---

## 7. Build Sequence (How to Add This)

### Step 1 — Build the REST API first (no AI yet)
Get your core modules working as clean REST APIs. This is the foundation everything else calls.

### Step 2 — Add MCP server (Week 1-2 of Phase 2)
```bash
npm install @modelcontextprotocol/sdk
```
Write an MCP server that wraps your 10 most-used API endpoints as tools.
Test it locally with Claude Desktop (add to `claude_desktop_config.json`).

### Step 3 — Ship MCP server as open-source
```
@adcmp/mcp-server (npm package)
```
Open-source MCP servers get adoption fast — the community installs them in their Claude setups.
This is free distribution.

### Step 4 — Add Agentic workflows as premium features
- Campaign Monitor Agent (daily summary + auto-pause) → paid tier
- Creative Refresh Advisor → paid tier
- Brief-to-Campaign Agent → paid tier

---

## 8. Competitive Moat This Creates

| Capability | Legacy CMPs | Your Plugin |
|-----------|-------------|-------------|
| Creative management | Yes | Yes |
| Campaign management | Partial | Yes |
| MCP server | No | Yes |
| Conversational control | No | Yes (via MCP) |
| Autonomous agents | No | Yes |
| Open AI-native | No | Yes |
| Embeddable | Limited | Yes (core design) |

Being **AI-native from day one** means you're not retrofitting intelligence later.
Legacy players (Celtra, Flashtalking) have decade-old architectures that make this very hard.

---

## 9. Quick Wins for MVP

Even before building agents, MCP adds immediate value:

1. **MCP Tool: get_campaign_performance** — Campaign managers ask Claude for their numbers in natural language instead of navigating dashboards.

2. **MCP Tool: get_asset_spec_validation** — Designers ask Claude "is this banner ready for Google Display?" without logging in.

3. **MCP Tool: list_pending_approvals** — Reviewers get a daily briefing from Claude: "You have 3 creatives awaiting your approval."

These three tools alone take **30-60 minutes to build** on top of your REST API and immediately make the product feel modern.
