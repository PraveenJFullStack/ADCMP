# Real-World Case Study — XX TDA (Targeting Display Ads)

> Source: Internal XX documents — TDA Onboarding PPT, Creative Submission & Reserves, Tenancy Ad Overview
> This case study grounds the ADCMP plugin concepts in a production ad platform used by 1,500+ merchants.

---

## 1. What TDA Is (and Why It's Relevant)

TDA is XX Ichiba's **self-serve targeting display ad product** for marketplace merchants.
It is a real, operating example of everything our plugin needs to manage:

| TDA Component | Plugin Module It Maps To |
|---------------|--------------------------|
| PRMM (merchant campaign UI) | Campaign Builder |
| Creative submission flow | Creative Asset Manager |
| Manual review by review team | Approval Workflow Engine |
| AdPlats (staff ad management) | Admin / Distribution layer |
| RUNA (delivery platform) | Platform Connector |
| IDB (BI reporting tool) | Analytics Aggregation |
| Creative spec validation | Spec Validation Engine |
| AI Banner Generation (Ph1/Ph2/Ph3) | AI Creative Generation Module |

---

## 2. The TDA System Architecture (AS-IS)

```
MERCHANT (on RMS/PRMM)
  │
  ├── Creates Campaign → fills targeting form → sends order request
  ├── Submits Creatives (4 image formats manually) → PRMM
  └── Views Performance Reports (hourly)

        │ campaign + creative data
        ▼

ADPLATS (Staff-side system)
  ├── Frame / location / format management
  ├── Pricing management
  └── Manuscript (creative) management

        │
        ▼

CMP (Creative Review Tool)
  ├── Review team manually checks each creative
  ├── Check: Logo, Image quality, Text font, Text size
  ├── Capacity: 10-12 campaigns per reviewer per day
  └── 3,000+ campaigns in backlog → bottleneck

        │ approved creatives
        ▼

RUNA (Delivery Platform)
  ├── Receives order + targeting + approved creatives
  ├── Segments from DMP (daily batch)
  ├── AIRIS (lookalike targeting)
  ├── DeepCDNA (AI auto-targeting)
  └── Delivers ads on Ichiba Web, App, Group Media

        │ performance data
        ▼

IDB (BI Tool) → PRMM Performance Reports → Merchant
```

---

## 3. The Creative Review Bottleneck — The Core Problem

This is the **exact pain point** our plugin's Approval Workflow and AI Review modules solve.

### AS-IS (Current State)
```
Merchant creates banner using own tool
  → Downloads 4 image formats manually
  → Uploads to PRMM
  → Review team manually checks (10-12/day per reviewer)
  → 3,000+ campaigns queued
  → High delay → delayed campaign delivery → lost merchant revenue
```

### Pain Points Quantified
| Problem | Impact |
|---------|--------|
| Manual review: 10-12 campaigns/day/reviewer | 3,000+ campaign backlog |
| No spec enforcement at upload | Wrong formats submitted, resubmission loop |
| Merchants use own tools (no standard) | Inconsistent quality, fails review guidelines |
| No creative reuse feature | Same reviewed creative re-reviewed again |

### TO-BE (Solution Path — directly mirrors our plugin)

**Solution A (Phase 1):** Creative Reuse
- Merchants can reuse previously-reviewed and approved creatives
- Review team can filter by "previously approved" status → auto-pass or minimal check
- Maps to our plugin: **Asset Library with approval status + reuse flag**

**Solution B (Phase 2):** In-Platform Creative Generation (PRMM Creative Tool)
- Merchants create banners inside PRMM (not external tools)
- System validates against review guidelines at creation time
- Maps to our plugin: **AI Banner Tool + Spec Validator embedded in upload flow**

---

## 4. TDA Ad Formats & Specs (Real Production Specs)

These are the **4 banner formats** merchants must submit for TDA:

| Format | Dimensions | Notes |
|--------|-----------|-------|
| Standard Banner 1 | 1280×200 | Wide leaderboard (desktop) |
| Standard Banner 2 | 400×800 | Tall rectangle (sidebar) |
| Standard Banner 3 | 480×360 | Medium rectangle |
| Standard Banner 4 | 880×320 | Wide medium (app) |

### Tenancy Ad Format Components (from AdPlats)
Each ad unit has structured components:

| Component | Spec |
|-----------|------|
| Logo | Required, brand logo |
| Image | 300×300 px |
| Text (headline) | Max 20 characters |
| Choice/CTA | Button element |
| Price | Dynamic field |
| Point back rate | 2-character text |
| Shop name | Required |

**Plugin implication:** Spec validation must handle both free-form banner specs AND structured component-based ad formats.

---

## 5. TDA Charging Model & Campaign Parameters

| Parameter | Details |
|-----------|---------|
| Charging model | vCPM (Viewable CPM) — paid per viewable impression |
| Minimum budget | JPY 50,000 per campaign |
| Bid range | JPY 0.75 – 10.00 per viewable impression |
| Delivery term | 4 days to 1 month |
| Request window | Up to 31 days in advance |
| Delivery pace | Immediate or Even delivery |
| Campaign status | Can stop/restart freely during term |

**Plugin implication for Campaign Builder:**
- Budget minimum validation (JPY 50,000)
- Bid range validator (0.75–10.00)
- Date range validator (4-day minimum, 31-day max advance booking)
- Delivery pace selector (immediate vs even)

---

## 6. TDA Targeting Types (Production Segments)

This is the real targeting model that informs our plugin's targeting configuration module:

### Targeting Modes
| Mode | Description | Best For |
|------|-------------|---------|
| AUTO (DeepCDNA) | AI-driven targeting, no manual setup | All sizes, best ROI |
| Custom / Manual | Merchant picks specific segments | Control-focused merchants |
| Retargeting | Purchase or view history of own shop | Maximizing existing customers |
| Lookalike (AIris) | Users similar to past buyers, ML-driven | Finding new customers |

### Custom Targeting Segment Dimensions
| Category | Options |
|----------|---------|
| Gender | Male / Female |
| Age | 20s through 70s |
| Location | 47 prefectures (Japan) |
| Membership rank | Gold, Platinum, etc. |
| View/Purchase history | By genre (Ladies Fashion, Electronics, etc.) |
| Life stage | Annual income, living arrangement, car ownership |
| Events | Father's Day, Valentine's, Halloween, New Year, etc. |

### Targeting Purpose → Segment Mapping
| Goal | Targeting Approach |
|------|--------------------|
| Brand Awareness | Demographics (age, gender, rank) |
| Interest Promotion | Genre view/purchase history |
| New User Acquisition | Lookalike (AIris) expansion |
| Repeat Purchase | Retargeting — 3+ purchase history |

---

## 7. KPI Framework (Production Metrics)

TDA uses a **vIMP-based KPI tree** — the authoritative example of how ad metrics connect:

```
Ad Sales
  = vIMP × vROAS
  = vIMP × (vGMS / vIMP)

vIMP (Viewable Impressions)
  = Ad Request × Fill Rate × View Rate

Ad Request = # campaigns × budget per shop × consumption rate

Key derived KPIs:
  vCTR  = Clicks / vIMP
  vCPM  = Spend / vIMP × 1000
  CPC   = Spend / Clicks
  vROAS = Revenue / Spend (720h attribution window)
  vCVR  = Conversions / Clicks
```

### Production KPI Benchmarks (from TDA data, 2024–2025)
| Metric | Typical Range |
|--------|-------------|
| vCTR | 0.87% – 1.17% |
| vCPM | JPY 700 – 1,200 |
| CPC | JPY 62 – 142 |
| vROAS | 10× – 26× |
| View Rate | 6.7% – 16.5% |
| Fill Rate | 85% – 103% |

**Note:** View Rate = vIMP / Total Impressions. A 50%+ pixel, 1-second threshold defines a "viewable impression" (same as MRC standard).

**Plugin implication:** Our analytics dashboard must support both standard IAB metrics AND vIMP-based metrics for marketplace ad platforms.

---

## 8. AI Banner Generation — Production Roadmap (TDA's Real Journey)

This is the most directly relevant section — TDA is already building what our plugin needs. Study it carefully.

### Phase 1 (Launched March 2025 — β)
**Goal:** Generate review-compliant banners so they skip creative review.

**Flow:**
```
1. Merchant selects Item + Logo from RMS catalog
2. Merchant selects Template Design (pre-built category templates)
3. Merchant inputs Ad Slogan (copy)
4. System generates banner using:
   - RMS Item Image (product photo from listing)
   - Shop Logo
   - Prompt Templates (system-built)
   - Title & Catch copy (AI-generated)
5. Preview → Submit directly (no creative review needed)
   → Guideline check still required (automated)
```

**Key insight:** AI-generated banners **bypass the manual review step** because they are built within known constraints. This is the same logic as our Spec Validator + AI Generator combo.

### Phase 2 (September 2025)
**Goal:** Improve adoption by improving banner quality.

**New capabilities:**
- New templates by category (industry-specific designs)
- Custom image support (merchant uploads their own image, not just RMS product image)
- Custom logo option
- Image segmentation (isolate product from background)
- More output variants (multiple design options)
- Custom text editor with font color, line breaks, aspect ratio adjustments
- New backgrounds

### Phase 3 (Planned 2026)
**Goal:** Improve performance via personalized banners.
- Per-user banner personalization based on audience segment
- This is DCO (Dynamic Creative Optimization) applied to marketplace ads

### AI Review (Parallel track)
- **AI Guideline Review Ph1 (Q3 2024):** Automates creative review checklist items
- **AI Guideline Review Ph3 (Q4 2025):** Further automation, reducing manual review to near-zero
- Maps to our plugin: **Brand Safety & Compliance AI check before approval submission**

---

## 9. Tenancy Ad — Fixed Inventory Model

This is a different ad type from TDA but equally important for our plugin:

### What Tenancy Ad Is
- **Fixed cost, fixed duration** display advertising (NOT auction-based)
- Merchant buys an **ad frame** (specific slot on page) for a specific period
- Charged by display period, not by impressions or clicks
- Used for brand awareness, event promotions

### How It Differs from TDA
| | TDA | Tenancy Ad |
|--|-----|------------|
| Pricing | vCPM (bid-based) | Fixed period fee |
| Targeting | Audience segments | Fixed placement, all visitors |
| Creative | Merchant banners | Structured components (logo+image+text) |
| Goal | Performance / sales | Awareness / brand visibility |
| Flexibility | Stop/start anytime | Fixed commitment |
| Revenue contribution | ~78% | ~22% of total ad sales |

### Tenancy Ad System Flow
```
CWD (commercial) → Creates frames/locations in AdPlats
Merchant → Purchases frame via PRMM EC → Submits creative → Review (CMP) → Delivery (Rasta)
```

**Plugin implication:** Our Campaign Builder must support **both** auction/bidding-based campaigns (TDA) AND reservation/fixed-inventory campaigns (Tenancy), as both exist in production.

---

## 10. Key Learnings for Our Plugin

### What TDA Validates in Our Design

| Our Plugin Design Choice | TDA Evidence |
|--------------------------|-------------|
| Approval workflow is core | Review team is the #1 bottleneck — 3,000+ backlog |
| AI generation bypasses review | TDA AI banners skip review if spec-compliant |
| Creative reuse feature needed | TDA explicitly lists reuse as Solution A |
| Spec validation at upload | TDA's entire Phase 2 is about build-time validation |
| Both auction and reservation models needed | TDA + Tenancy both exist in same merchant system |
| vIMP / viewability metrics needed | TDA's entire KPI tree is vIMP-based |

### What TDA Reveals We Should Add

| Gap Found | What to Add to Plugin |
|-----------|-----------------------|
| Review capacity is the bottleneck | **AI-assisted review** as a module (not just approval routing) |
| Merchants need guideline-aware creation | **Review checklist embedded in creative builder** |
| Item/product feed integration | **Product catalog connector** (pull product images, prices) |
| Creative reuse tracking | **Campaign-to-creative usage map** in asset library |
| Structured ad formats (logo+text+image) | **Component-based ad builder** alongside free-form banner upload |

### Production Numbers to Design For
- 1,500–1,770 active merchants/month (scale target for plugin)
- 3,000+ campaigns needing review (approval workflow must handle batch)
- 10-12 reviews/day/reviewer (without AI — benchmark for ROI of AI review)
- vCPM range JPY 700–1,200 (bidding UI validation range)
- 4 mandatory banner sizes per campaign (multi-size asset grouping needed)
