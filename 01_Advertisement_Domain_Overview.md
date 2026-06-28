# Advertisement Domain — In-Depth Overview

## 1. What Is Advertising?

Advertising is the **paid, non-personal communication** of information about a product, service, brand, or idea, directed at a target audience through a medium, by an identified sponsor, with the goal of influencing awareness, attitude, or behavior.

---

## 2. The Advertising Ecosystem — Key Players

```
Advertiser (Brand)
      │
      ▼
 Agency / In-House Team
   ├── Creative Agency  → conceives, produces ad assets
   ├── Media Agency     → plans, buys, optimizes media
   └── Trading Desk     → programmatic buying layer
      │
      ▼
 Ad Tech Stack
   ├── DSP (Demand-Side Platform)   → automated media buying
   ├── SSP (Supply-Side Platform)   → publisher inventory management
   ├── Ad Exchange                  → real-time auction marketplace
   ├── DMP / CDP                    → audience data layer
   ├── Ad Server                    → serves, tracks, reports
   └── Creative Management Platform → manages ad creatives
      │
      ▼
 Publisher / Media Owner
   (Website, App, CTV, OOH, etc.)
      │
      ▼
 Consumer / Audience
```

---

## 3. Types of Advertising

### By Channel
| Channel | Examples |
|---------|---------|
| Digital Display | Banner ads, rich media, DOOH |
| Search (SEM) | Google Ads, Bing Ads |
| Social | Meta, LinkedIn, TikTok, X |
| Video | YouTube, CTV, pre-roll |
| Audio | Spotify, podcast ads |
| Native | Sponsored content, in-feed |
| Email | Newsletters, promotional |
| Out-of-Home (OOH) | Billboards, transit |
| Print | Newspapers, magazines |
| Broadcast | TV, radio |
| In-App / Gaming | Mobile ads, rewarded video |

### By Buying Model
| Model | Description |
|-------|-------------|
| Programmatic | Automated real-time bidding (RTB) via DSP |
| Direct / Reservation | Guaranteed inventory bought directly from publisher |
| Private Marketplace (PMP) | Invite-only programmatic deal |
| Programmatic Guaranteed (PG) | Fixed price, guaranteed impression volume |

---

## 4. The Ad Delivery Lifecycle

```
1. PLANNING
   Campaign objective → KPIs → target audience → budget → channel mix

2. CREATIVE PRODUCTION
   Concept → Copywriting → Design → Animation → QA → Trafficking

3. CAMPAIGN SETUP
   Campaign → Line Items → Ad Groups → Targeting → Bidding → Creative Attachment

4. TRAFFICKING
   Creative tags generated → sent to publisher / DSP → ad server configured

5. DELIVERY / SERVING
   User visits publisher → ad request sent → auction (if RTB) → winning ad served

6. MEASUREMENT
   Impressions, Clicks, CTR, VCR, ROAS, Conversions tracked

7. OPTIMIZATION
   A/B test creatives, adjust bids, reallocate budget, pause underperformers

8. REPORTING & INSIGHTS
   Dashboards, attribution reports, audience insights, post-campaign analysis
```

---

## 5. Core Advertising Metrics (KPIs)

### Awareness
- **Impressions** — total ad displays
- **Reach** — unique users who saw the ad
- **Frequency** — average times a user saw the ad
- **CPM** (Cost per Mille) — cost per 1,000 impressions

### Engagement
- **Clicks / CTR** (Click-Through Rate) = Clicks / Impressions × 100
- **VCR** (Video Completion Rate)
- **Engagement Rate** — likes, shares, comments / impressions
- **Dwell Time** — time spent with the ad

### Conversion
- **CPC** (Cost per Click)
- **CPA** (Cost per Acquisition/Action)
- **CVR** (Conversion Rate) = Conversions / Clicks × 100
- **ROAS** (Return on Ad Spend) = Revenue / Ad Spend
- **ROI** = (Revenue - Cost) / Cost × 100

### Viewability
- **In-View Rate** — % of ads actually seen (MRC: 50% pixels, 1 second)
- **Active View** — Google's viewability metric

---

## 6. Audience Targeting

### Data Types
- **1st-Party Data** — brand's own CRM, website, app behavioral data
- **2nd-Party Data** — partner-shared first-party data
- **3rd-Party Data** — aggregated data from DMPs (declining post-cookie)
- **Contextual Data** — page/content topic targeting (cookieless-safe)
- **Cohort Data** — Privacy Sandbox, Topics API

### Targeting Methods
| Method | Description |
|--------|-------------|
| Demographic | Age, gender, income, education |
| Geographic | Country, city, zip, radius |
| Behavioral | Past actions, purchase intent |
| Contextual | Content of the page |
| Lookalike | Similar to existing customers |
| Retargeting | People who interacted with brand |
| Keyword | Search intent matching |
| Device | Mobile, desktop, CTV |
| Dayparting | Time-of-day scheduling |

---

## 7. Ad Formats

### Display
- Standard banners: 300×250, 728×90, 160×600, 320×50 (mobile)
- Rich media: expandable, interstitial, video-in-banner
- HTML5 animated
- DCO (Dynamic Creative Optimization) — personalized at scale

### Video
- In-stream: pre-roll, mid-roll, post-roll
- Out-stream: in-feed, in-article
- Rewarded video (mobile/gaming)
- CTV/OTT: connected TV ads

### Native
- In-feed articles
- Sponsored listings
- Recommendation widgets (Taboola, Outbrain)

### Search
- Text ads (headline, description, URL)
- Shopping / Product Listing Ads (PLAs)
- Responsive Search Ads (RSA)

### Social
- Single image, carousel, collection
- Stories / Reels
- Lead gen forms

---

## 8. Attribution Models

| Model | Logic |
|-------|-------|
| Last-Touch | 100% credit to last touchpoint |
| First-Touch | 100% credit to first touchpoint |
| Linear | Equal credit across all touchpoints |
| Time Decay | More credit to recent touchpoints |
| Position-Based (U-Shaped) | 40% first, 40% last, 20% middle |
| Data-Driven (Algorithmic) | ML-based, most accurate |

---

## 9. Privacy & Compliance

- **GDPR** (EU) — consent required for personal data
- **CCPA** (California) — opt-out rights
- **Apple ATT** — app tracking transparency (iOS 14.5+)
- **Cookie Deprecation** — Chrome third-party cookie phase-out
- **Brand Safety** — avoiding placement next to harmful content
- **Ad Fraud** — invalid traffic (IVT), bot traffic detection

---

## 10. Emerging Trends

| Trend | Description |
|-------|-------------|
| AI Creative | Generative AI for ad copy and visuals |
| DCO | Real-time personalized creative assembly |
| CTV/OTT | Premium video streaming advertising |
| Retail Media | Amazon, Walmart, Kroger ad networks |
| Privacy-First | Contextual, cohort-based, clean rooms |
| Attention Metrics | Beyond viewability to actual engagement |
| Sustainability | Carbon footprint of digital advertising |
