# Ad Tech Stack — Architecture & Components

## 1. The Programmatic Advertising Ecosystem

```
┌─────────────────────────────────────────────────────────────────┐
│                        ADVERTISER SIDE                          │
│  Brand → Agency → Trading Desk → DSP → Data Layer (DMP/CDP)    │
└────────────────────────────┬────────────────────────────────────┘
                             │  RTB Bid Request / Response
                             │  (OpenRTB protocol, ~100ms)
┌────────────────────────────▼────────────────────────────────────┐
│                        AD EXCHANGE                              │
│           (Google Ad Manager, Index Exchange, Magnite)          │
└────────────────────────────┬────────────────────────────────────┘
                             │
┌────────────────────────────▼────────────────────────────────────┐
│                        PUBLISHER SIDE                           │
│       SSP → Publisher Ad Server → Website / App / CTV          │
└─────────────────────────────────────────────────────────────────┘
```

---

## 2. Component Breakdown

### DSP (Demand-Side Platform)
**What it does:** Allows advertisers to buy ad impressions programmatically across multiple ad exchanges from a single interface.

**Key features:**
- Audience targeting engine
- Real-time bidding engine
- Budget management
- Frequency capping
- Creative trafficking
- Brand safety controls
- Reporting & analytics

**Examples:** Google DV360, The Trade Desk, Amazon DSP, Xandr, Verizon Media DSP

---

### SSP (Supply-Side Platform)
**What it does:** Helps publishers manage, sell, and optimize their ad inventory programmatically.

**Key features:**
- Floor price management
- Deal management (PMP, PG)
- Header bidding integration
- Yield optimization
- Inventory quality controls

**Examples:** Google Ad Manager, Magnite, PubMatic, Index Exchange, OpenX

---

### Ad Exchange
**What it does:** A technology-driven marketplace where DSPs and SSPs connect to facilitate real-time auctions.

**Auction types:**
- **First-price auction** — winner pays their exact bid
- **Second-price auction** — winner pays $0.01 above second-highest bid (largely deprecated)
- **Header bidding** — simultaneous auction across multiple buyers (replaces waterfall)

---

### DMP (Data Management Platform)
**What it does:** Aggregates, organizes, and activates audience data from multiple sources.

**Data types ingested:**
- 1st-party (CRM, website, app)
- 2nd-party (partner data)
- 3rd-party (data brokers)

**Declining due to:** Cookie deprecation, privacy regulations

**Examples:** Oracle BlueKai, Salesforce DMP (Krux), Lotame

---

### CDP (Customer Data Platform)
**What it does:** A persistent, unified customer database that makes data accessible to other systems.

**Key difference from DMP:**
| | DMP | CDP |
|--|-----|-----|
| Identity | Anonymous (cookie-based) | Known (PII-linked) |
| Data retention | Short-term | Long-term |
| Primary use | Media targeting | Customer experience |
| Privacy | Lower bar | GDPR/CCPA compliant |

**Examples:** Segment, mParticle, Treasure Data, Adobe CDP

---

### Ad Server
**What it does:** Stores, delivers, and tracks ad creatives. The system of record for campaign delivery.

**Advertiser-side (3rd-party):**
- Campaign Manager 360 (Google)
- Sizmek (Amazon)

**Publisher-side (1st-party):**
- Google Ad Manager (GAM / DFP)
- Freewheel (video)

**Core functions:**
- Creative storage and CDN delivery
- Decision logic (which ad to serve)
- Click and impression tracking
- Frequency management
- Conversion tracking
- Reporting (the authoritative number)

---

### Creative Management Platform (CMP)
**What it does:** End-to-end platform for producing, managing, and optimizing ad creatives.

**Key features:**
- HTML5 ad builder / template studio
- Asset library (DAM)
- Approval workflows
- Spec validation
- Platform distribution
- DCO engine
- Performance reporting

**Examples:** Celtra, Flashtalking, Storyteq, Bannerflow, Smartly.io

---

### Verification & Measurement
| Vendor | Focus |
|--------|-------|
| DoubleVerify (DV) | Brand safety, viewability, fraud |
| Integral Ad Science (IAS) | Brand safety, viewability, fraud |
| MOAT (Oracle) | Attention, viewability |
| Nielsen | Audience measurement, GRP |
| comScore | Cross-platform audience |
| Neustar / Transunion | Attribution, identity |

---

## 3. RTB (Real-Time Bidding) — Under the Hood

```
TIME: ~100 milliseconds

1. USER visits webpage
2. PUBLISHER'S AD TAG fires
3. SSP sends BID REQUEST to Ad Exchange
   (Contains: user data, page URL, ad slot specs, floor price)
4. Ad Exchange forwards to eligible DSPs
5. DSP evaluates:
   - Does this user match my targeting?
   - What is this impression worth?
   - Do I have creative to serve?
   → Calculates bid using:
     CPM_bid = eCPM model (historical CTR × CVR × value per conversion × 1000)
6. DSP responds with BID RESPONSE (or no-bid)
7. Ad Exchange runs auction → picks winner
8. WINNING DSP notified
9. AD SERVER serves creative to publisher
10. TRACKING pixels fire (impression, viewability)
11. REPORT updates in near-real-time
```

---

## 4. Campaign Manager 360 (CM360) — Deep Dive

CM360 is Google's advertiser-side ad server. It is the central hub for:

**Trafficking flow in CM360:**
```
Create Advertiser
  └── Create Campaign
        └── Create Placement (from publisher)
              └── Create Ad
                    └── Assign Creative
                          └── Generate Tag
                                └── Send tag to Publisher
```

**Key entities:**
| Entity | Description |
|--------|-------------|
| Advertiser | Brand / client account |
| Campaign | Date-bounded container |
| Site | Media vendor / publisher |
| Placement | Specific ad slot purchased |
| Ad | Creative + targeting rules |
| Creative | The actual ad asset |
| Floodlight | Conversion tracking tag |

---

## 5. DV360 (Display & Video 360) — Architecture

DV360 is Google's DSP. It integrates with CM360 for tracking and creative management.

```
DV360 Structure:
Partner
  └── Advertiser
        └── Campaign
              └── Insertion Order (IO)
                    └── Line Item
                          └── Creative (linked from CM360)
```

**Line Item targeting levers:**
- Audience lists
- Geography
- Device & OS
- Inventory source (exchange, deal)
- Viewability threshold
- Dayparting
- Language
- Content categories

---

## 6. Meta Ads Architecture

```
Business Manager
  └── Ad Account
        └── Campaign (Objective)
              └── Ad Set (Audience + Budget + Schedule)
                    └── Ad (Creative + CTA + URL)
```

**Meta's Advantage+ (AI automation):**
- Advantage+ Shopping Campaigns
- Advantage+ Audience (AI targeting)
- Dynamic Ads (product catalog)
- Advantage+ Creative (auto-variations)

---

## 7. Google Ads Architecture

```
Account
  └── Campaign (Type + Objective + Budget)
        └── Ad Group (Theme + Bid)
              └── Ad (RSA/DSA/Video)
              └── Keywords / Audiences / Placements
```

**Smart Campaign types:**
- Performance Max — fully automated, all channels
- Demand Gen — YouTube, Discover, Gmail
- Search — keyword-driven intent

---

## 8. Data Flows Summary

```
Pixel/Tag fires on advertiser site
      │
      ▼
Event data → Ad Server (Floodlight) → Attribution
      │
      ▼
Audience list created → DMP/CDP → DSP audience targeting
      │
      ▼
DSP bid data → Campaign reporting
      │
      ▼
All data → BI Layer (BigQuery, Looker, Tableau)
```
