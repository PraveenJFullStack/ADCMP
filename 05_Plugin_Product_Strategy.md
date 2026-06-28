# Campaign & Creative Management Plugin — Product Strategy

## 1. Problem Statement

Brands and agencies managing advertising across multiple platforms face:

| Pain Point | Impact |
|------------|--------|
| Fragmented campaign setup (separate UIs per platform) | High manual effort, errors |
| Creative asset scattered across tools (Drive, emails, Figma) | Version confusion, delays |
| No unified approval workflow | Bottlenecks, compliance risk |
| Spec management per platform is manual | Incorrect trafficking, wasted spend |
| No cross-platform reporting in one view | Blind spots, slow optimization |
| Rebuilding same structures for every new campaign | Wasted time |
| No creative performance feedback loop | Inefficient creative investment |

---

## 2. Product Vision

> **A lightweight, embeddable plugin that any ad platform, CRM, or marketing tool can integrate to manage campaigns and creatives end-to-end — from brief to performance — without rebuilding the wheel.**

### Core Value Propositions
1. **Plug in anywhere** — SDK-first, white-label ready
2. **Single source of truth** for creatives and campaign structures
3. **Approval workflow baked in** — no more email chains
4. **Spec-aware** — validates assets before trafficking
5. **Performance feedback loop** — know which creative works

---

## 3. Target Users

### Primary Users
| Persona | Need |
|---------|------|
| Media Planner | Build campaign structures fast, across platforms |
| Campaign Manager | Monitor delivery, make bid/budget changes |
| Creative Producer | Manage assets, versions, approvals |
| Brand Manager | Ensure creative compliance, approve before going live |
| Performance Analyst | See creative and campaign data in one view |

### Secondary Users
| Persona | Need |
|---------|------|
| Agency Account Director | Portfolio-level visibility |
| Legal / Compliance Officer | Approve claims, disclaimers |
| Developer / Integrator | Embed plugin into existing tools |

---

## 4. Plugin Architecture

### Deployment Models

```
┌──────────────────────────────────────────────────────────────┐
│                    INTEGRATION OPTIONS                        │
├──────────────┬───────────────┬───────────────────────────────┤
│  Web Plugin  │  iFrame Embed │  Native SDK (React component) │
│  (JS snippet)│  (white-label)│  (full React/Vue integration) │
└──────────────┴───────────────┴───────────────────────────────┘
```

### System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    PLUGIN FRONTEND                          │
│  React component library (publishable as npm package)       │
│  ├── Campaign Builder UI                                    │
│  ├── Creative Studio UI                                     │
│  ├── Workflow / Approval UI                                 │
│  └── Analytics Dashboard UI                                 │
└──────────────────────────┬──────────────────────────────────┘
                           │ REST API / GraphQL / WebSocket
┌──────────────────────────▼──────────────────────────────────┐
│                    PLUGIN BACKEND (API)                     │
│  ├── Campaign Service                                       │
│  ├── Creative Asset Service                                 │
│  ├── Approval Workflow Service                              │
│  ├── Spec Validation Service                                │
│  ├── Distribution Service (platform connectors)            │
│  ├── Analytics Aggregation Service                         │
│  └── Notification Service                                  │
└──────────────────────────┬──────────────────────────────────┘
                           │
         ┌─────────────────┼─────────────────┐
         ▼                 ▼                 ▼
    Platform APIs      Storage Layer     Data Layer
    (Google, Meta,     (S3/CDN for       (PostgreSQL,
     DV360, TTD...)    assets)            Redis, S3)
```

---

## 5. Core Modules

### Module 1: Campaign Builder
**What it does:** Create, clone, and manage campaign structures

**Key features:**
- Template library (pre-built campaign structures)
- Multi-platform field mapping (one form → syncs to Google, Meta, DV360)
- Bulk operations (clone, pause, budget adjust)
- Campaign calendar view
- Status management and audit log

**API endpoints:**
```
POST   /campaigns                    Create campaign
GET    /campaigns/{id}               Get campaign details
PUT    /campaigns/{id}               Update campaign
POST   /campaigns/{id}/clone         Clone campaign
GET    /campaigns?status=active      List campaigns with filters
POST   /campaigns/bulk               Bulk operations
GET    /campaigns/{id}/changelog     Audit trail
```

---

### Module 2: Creative Asset Manager
**What it does:** Centralized library for all ad creative assets

**Key features:**
- Upload with automatic metadata extraction (dimensions, format, file size)
- Folder structure / tagging
- Version history per asset
- Spec checker on upload (validates against platform requirements)
- Preview renderer (see how ad looks on placement)
- CDN delivery URLs for trafficking
- Usage tracking (which campaigns use which creatives)

**Asset Object Model:**
```json
{
  "id": "ast_123",
  "name": "Nike_RunFast_300x250_V2.html",
  "type": "html5",
  "dimensions": { "width": 300, "height": 250 },
  "file_size_kb": 148,
  "created_at": "2024-01-15T10:00:00Z",
  "versions": [
    { "version": 1, "url": "...", "created_by": "designer@agency.com" },
    { "version": 2, "url": "...", "created_by": "designer@agency.com" }
  ],
  "tags": ["display", "Q4", "Nike", "RunFast"],
  "campaigns": ["cmp_456", "cmp_789"],
  "approval_status": "approved",
  "spec_validation": {
    "google_display": "pass",
    "meta_feed": "fail",
    "dv360": "pass"
  }
}
```

---

### Module 3: Approval Workflow Engine
**What it does:** Multi-step, role-based approval process for creatives and campaigns

**Workflow Configuration (per tenant):**
```
Step 1: Creator submits → Status: "Pending Brand Review"
Step 2: Brand Manager reviews
  → Approve → Step 3
  → Reject with comments → back to Creator
Step 3: Legal reviews (if claims present)
  → Approve → Step 4
  → Conditional approve (with edits) → back to Creator
Step 4: Campaign Manager trafficking sign-off
  → Approve → Status: "Ready to Traffic"
```

**Workflow is configurable:**
- Skip steps based on creative type
- Auto-approve if low risk (static image, pre-approved template)
- SLA alerts (reviewer not actioned in X hours)
- Bulk approve for minor variants

**Key entities:**
```json
{
  "workflow_run_id": "wfr_456",
  "asset_id": "ast_123",
  "current_step": 2,
  "current_assignee": "brand.manager@company.com",
  "history": [
    { "step": 1, "action": "submitted", "by": "designer@agency.com", "at": "..." },
    { "step": 2, "action": "changes_requested", "comment": "Logo too small", "by": "brand@company.com" }
  ],
  "status": "changes_requested",
  "due_date": "2024-01-17T18:00:00Z"
}
```

---

### Module 4: Spec Validation Engine
**What it does:** Validates creative assets against platform-specific requirements automatically

**Spec Database (maintained and updated):**
```json
{
  "platform": "google_display",
  "format": "html5",
  "rules": [
    { "rule": "max_file_size_kb", "value": 150 },
    { "rule": "allowed_dimensions", "values": ["300x250","728x90","160x600","320x50"] },
    { "rule": "max_animation_loops", "value": 3 },
    { "rule": "must_have_click_tag", "value": true },
    { "rule": "max_initial_load_kb", "value": 50 }
  ]
}
```

**Validation result:**
```json
{
  "asset_id": "ast_123",
  "platform": "google_display",
  "passed": false,
  "issues": [
    { "rule": "max_file_size_kb", "actual": 163, "limit": 150, "severity": "error" },
    { "rule": "max_animation_loops", "actual": 5, "limit": 3, "severity": "warning" }
  ]
}
```

---

### Module 5: Platform Distribution
**What it does:** Push campaigns and creatives to ad platforms via their APIs

**Supported connectors:**
- Google Ads API
- Meta Marketing API
- DV360 API (Bid Manager API)
- The Trade Desk API
- Campaign Manager 360 API
- LinkedIn Marketing API
- TikTok Marketing API

**Distribution flow:**
```
Plugin Internal → Transform to platform schema → Platform API call → Store platform ID → Track sync status
```

**Sync status model:**
```json
{
  "campaign_id": "cmp_456",
  "platform_syncs": [
    { "platform": "google_ads", "platform_campaign_id": "123456789", "status": "synced", "last_sync": "..." },
    { "platform": "meta", "platform_campaign_id": "987654321", "status": "pending", "error": null },
    { "platform": "dv360", "status": "failed", "error": "Invalid targeting combination" }
  ]
}
```

---

### Module 6: Analytics Aggregation
**What it does:** Pull performance data from all platforms, normalize, and surface creative + campaign insights

**Metrics normalized across platforms:**
| Plugin Metric | Google Ads | Meta | DV360 |
|---------------|-----------|------|-------|
| impressions | impressions | impressions | impressions |
| clicks | clicks | clicks | clicks |
| ctr | ctr | ctr | click_rate |
| spend | cost | spend | media_cost |
| conversions | conversions | actions | conversions |
| cpa | cost_per_conversion | cost_per_action | cpa |

**Aggregation pipeline:**
```
Platform APIs (pull daily/hourly)
      │
Normalization layer (common schema)
      │
Enrichment (creative metadata join)
      │
Storage (time-series DB or warehouse)
      │
API layer (serve to dashboard)
```

---

## 6. Plugin Integration Options

### Option A: JavaScript Snippet (Lightest)
```html
<script src="https://cdn.adplugin.io/v1/sdk.js"></script>
<script>
  AdPlugin.init({
    apiKey: 'your_api_key',
    tenantId: 'your_tenant_id',
    container: '#ad-plugin-mount',
    modules: ['creative-manager', 'approval-workflow']
  });
</script>
<div id="ad-plugin-mount"></div>
```

### Option B: React Component (Most Common for SaaS)
```jsx
import { AdPlugin } from '@adplugin/react';

function App() {
  return (
    <AdPlugin
      apiKey="your_api_key"
      tenantId="your_tenant_id"
      modules={['campaign-builder', 'creative-manager']}
      theme={{ primaryColor: '#0057FF' }}
      onCampaignCreated={(campaign) => console.log(campaign)}
    />
  );
}
```

### Option C: REST API Only (Backend Integration)
- Full headless mode
- Integrate into any existing UI
- Full control over UX
- Highest development effort

### Option D: iFrame Embed (White-Label)
```html
<iframe
  src="https://app.adplugin.io/embed?tenant=xyz&module=creative"
  width="100%"
  height="800px"
  frameborder="0">
</iframe>
```

---

## 7. Multi-Tenancy Design

```
Tenant A (Agency ABC)
  ├── Client 1 (Brand X)
  │     ├── Campaign 1
  │     └── Campaign 2
  └── Client 2 (Brand Y)
        └── Campaign 3

Tenant B (In-House Marketing Team)
  └── Campaign 4
```

**Isolation model:** Database-level tenant isolation (row-level security or schema-per-tenant)

**Permissions model:**
| Role | Campaign | Creative | Approval | Reports | Settings |
|------|----------|----------|----------|---------|---------|
| Admin | Full | Full | Full | Full | Full |
| Campaign Manager | Full | View | - | Full | - |
| Creative Producer | View | Full | Submit | View | - |
| Reviewer / Approver | View | View | Approve/Reject | View | - |
| Analyst | View | View | - | Full | - |
| Read-Only | View | View | - | View | - |

---

## 8. Technology Stack (Recommended)

### Frontend
- **Framework:** React + TypeScript
- **State Management:** Zustand or React Query
- **UI Components:** Radix UI + Tailwind CSS (white-label ready)
- **Package:** Publishable as npm package (@company/adplugin-react)
- **Build:** Vite + Rollup (dual CJS + ESM output)

### Backend
- **Runtime:** Node.js (TypeScript) or Go
- **API:** REST + GraphQL (for flexible querying)
- **Auth:** JWT + OAuth2 (platform API tokens stored encrypted)
- **Queue:** Bull/Redis for async jobs (platform syncs, validations)

### Storage
- **Database:** PostgreSQL (campaigns, metadata, approvals)
- **Cache:** Redis (sessions, rate limiting, hot data)
- **Asset Storage:** AWS S3 + CloudFront CDN
- **Time-Series:** ClickHouse or BigQuery (analytics data)

### Infrastructure
- **Hosting:** AWS / GCP (containerized, Kubernetes)
- **CI/CD:** GitHub Actions
- **Monitoring:** Datadog / Grafana

---

## 9. Monetization Models

| Model | Description | Best For |
|-------|-------------|---------|
| SaaS Subscription | Per seat / per month | Agencies, brands |
| Platform License | White-label + annual fee | Enterprise SaaS companies |
| Usage-Based | Per campaign or per asset | Small teams, occasional use |
| Freemium | Free tier + paid features | Bottom-up growth |
| Revenue Share | % of media managed | Embedded in media tools |

---

## 10. Build Roadmap (MVP → Scale)

### Phase 1 — MVP (3 months)
- [ ] Creative Asset Library (upload, tag, search, version)
- [ ] Basic Approval Workflow (configurable steps, email notifications)
- [ ] Spec Validator (top 5 platforms, top 10 formats)
- [ ] React component + REST API
- [ ] Multi-tenancy (basic)

### Phase 2 — Campaign Management (3 months)
- [ ] Campaign Builder (internal structure)
- [ ] Google Ads connector
- [ ] Meta connector
- [ ] Basic analytics dashboard (pull data, show it)
- [ ] Role-based access control

### Phase 3 — Intelligence & Scale (6 months)
- [ ] DV360 + TTD connectors
- [ ] DCO engine (template + data feed assembly)
- [ ] Cross-platform analytics normalization
- [ ] Creative fatigue alerts
- [ ] Campaign cloning & bulk operations
- [ ] Webhook system (notify host apps on events)

### Phase 4 — Ecosystem (6 months)
- [ ] Marketplace of approval templates
- [ ] AI creative brief generator
- [ ] AI creative performance prediction
- [ ] Brand safety pre-check (AI image/text scanning)
- [ ] Native integrations (Figma plugin, Slack notifications)
