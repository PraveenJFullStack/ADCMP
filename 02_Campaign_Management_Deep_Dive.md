# Campaign Management — Deep Dive

## 1. What Is a Campaign?

A **campaign** is a coordinated set of ad activities unified by a single **objective**, **budget**, **time window**, and **target audience**. It is the top-level container in every ad platform's hierarchy.

---

## 2. Campaign Hierarchy (Universal)

```
CAMPAIGN
  └── AD SET / LINE ITEM / AD GROUP
          └── AD / CREATIVE
```

### Platform-Specific Naming

| Platform | Level 1 | Level 2 | Level 3 |
|----------|---------|---------|---------|
| Google Ads | Campaign | Ad Group | Ad |
| Meta Ads | Campaign | Ad Set | Ad |
| DV360 / DSP | Campaign | Insertion Order (IO) | Line Item → Creative |
| The Trade Desk | Campaign | Ad Group | Ad |
| LinkedIn | Campaign Group | Campaign | Ad |
| TikTok | Campaign | Ad Group | Ad |

---

## 3. Campaign Objectives

Mapped to the **Marketing Funnel**:

```
AWARENESS
  ├── Brand Awareness
  ├── Reach
  └── Video Views

CONSIDERATION
  ├── Traffic
  ├── Engagement
  ├── Lead Generation
  └── App Installs

CONVERSION
  ├── Sales / Purchases
  ├── Store Visits
  └── Catalog Sales
```

The objective dictates:
- Bidding strategy available
- Ad formats allowed
- Optimization signals used by the algorithm

---

## 4. Budget Management

### Budget Types
| Type | Description | Use Case |
|------|-------------|---------|
| Daily Budget | Max spend per day | Consistent daily pacing |
| Lifetime Budget | Total for campaign duration | Event-based, fixed window |
| Monthly Budget | Cap for calendar month | Google Ads specific |

### Pacing Strategies
- **Standard** — even distribution across the flight
- **Accelerated** — spend as fast as possible (deprecated in Google, still in some DSPs)
- **Front-loaded / Back-loaded** — weighted toward start or end
- **Goal-based** — algorithmic pacing to hit a conversion target

### Budget Hierarchy
```
Campaign Budget
  ├── IO / Ad Set Budget (subset of campaign)
  │     ├── Line Item Budget (optional sub-limit)
  │     └── ...
  └── Shared Budgets (across campaigns)
```

---

## 5. Bidding Strategies

### Manual Bidding
- **CPM** (Cost per Mille) — pay per impression
- **CPC** (Cost per Click) — pay per click
- **CPV** (Cost per View) — pay per video view

### Automated / Smart Bidding
| Strategy | Goal | Used When |
|----------|------|-----------|
| Target CPA | Acquire conversions at target cost | Conversion campaign with data |
| Target ROAS | Maximize revenue for budget | E-commerce |
| Maximize Conversions | Get most conversions | Budget-limited campaigns |
| Maximize Clicks | Drive traffic | Top-of-funnel |
| Target Impression Share | Control SERP presence | Brand campaigns |
| Enhanced CPC (ECPC) | Adjusts manual bids | Transitioning to automation |
| vCPM | Viewable CPM | Awareness |

---

## 6. Targeting Configuration

### Audience Targeting
```
INCLUSION                      EXCLUSION
├── Custom Audiences           ├── Existing Customers
├── Lookalike Audiences        ├── Converted Users
├── Interest Segments          └── Brand Unsafe Contexts
├── In-Market Audiences
└── Retargeting Lists
```

### Inventory / Placement Targeting
- Specific websites / apps
- Topic / category targeting
- Keyword contextual
- Placement block lists / allow lists
- Brand safety categories (IAB, GARM)

### Technical Targeting
- Device type (mobile, desktop, tablet, CTV)
- Operating system / browser
- Internet connection type
- Viewability threshold
- Language

### Geographic Targeting
- Country → Region → City → ZIP → Radius
- Geo-fencing (real-time location)
- Store visit radius

---

## 7. Scheduling & Flighting

**Flight** — the campaign's active date range

**Dayparting** — scheduling ads to run only during specific hours/days

```
Example: Coffee brand
  Weekdays: 6am–9am (morning commute)
  Weekends: 8am–11am (leisurely browsing)
```

**Frequency Capping**
- Prevents ad fatigue
- e.g., max 3 impressions per user per day
- Defined at campaign, IO, or line item level

---

## 8. Campaign Workflow (End-to-End)

```
BRIEF
  Campaign objective, audience, budget, timeline, KPIs, brand guidelines
        │
STRATEGY
  Channel mix, audience segments, bidding strategy, creative approach
        │
SETUP
  Campaign structure → targeting → budgets → bid → pixel/tag implementation
        │
CREATIVE TRAFFICKING
  Assets received → specs check → upload → assign to line items → QA
        │
LAUNCH (GO LIVE)
  Internal QA → approval → activation
        │
MONITORING (DAY 0–7)
  Delivery pacing, CTR sanity, budget burn, auction win rates
        │
OPTIMIZATION (ONGOING)
  Bid adjustments, audience refinements, creative rotation, budget shifts
        │
REPORTING
  Weekly / end-of-campaign reports, insights, recommendations
        │
POST-CAMPAIGN ANALYSIS
  Attribution, incrementality, learnings for next campaign
```

---

## 9. Campaign States / Statuses

| Status | Meaning |
|--------|---------|
| Draft | Created but not submitted |
| In Review | Pending platform approval |
| Active | Live and serving |
| Paused | Temporarily stopped |
| Ended | Past end date |
| Budget Exhausted | Daily/lifetime budget hit |
| Limited | Serving but restricted (budget, audience) |
| Rejected | Failed platform policy review |

---

## 10. Key Campaign Management Operations

| Operation | Description |
|-----------|-------------|
| Clone / Duplicate | Copy campaign structure for A/B or new markets |
| Bulk Edit | Mass update bids, budgets, targeting |
| Labels / Tags | Organize campaigns for reporting |
| Versioning | Track changes over time |
| Approval Workflow | Multi-role review before activation |
| Alerts | Automated notifications on anomalies |
| Automated Rules | If CTR < X, pause; if budget < Y, notify |
| Change Log | Audit trail of all modifications |

---

## 11. Multi-Platform Campaign Challenges

| Challenge | Impact |
|-----------|--------|
| Different hierarchies per platform | Complex unified reporting |
| Different metric definitions | Inconsistent performance comparison |
| Creative specs vary | Separate asset versions needed |
| No unified frequency cap | User over-exposed across channels |
| Attribution fragmentation | Unclear what drove conversion |
| Manual trafficking | Error-prone, time-consuming |

---

## 12. Campaign Analytics & Reporting

### Essential Report Dimensions
- Campaign / Ad Set / Ad level
- Date range (daily, weekly, monthly)
- Channel / Platform
- Audience segment
- Creative / Ad format
- Geography
- Device type

### Derived Metrics
```
CTR = Clicks / Impressions × 100
CVR = Conversions / Clicks × 100
CPA = Spend / Conversions
ROAS = Revenue / Spend
CPM = (Spend / Impressions) × 1000
Frequency = Impressions / Reach
```
