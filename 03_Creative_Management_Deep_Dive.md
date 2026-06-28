# Creative Management — Deep Dive

## 1. What Is Creative Management?

**Creative Management** is the end-to-end process of **creating, storing, versioning, approving, distributing, and optimizing** ad creative assets across campaigns, channels, and audiences.

It bridges the gap between **marketing/design teams** (who produce content) and **media teams** (who run campaigns).

---

## 2. The Creative Lifecycle

```
BRIEF
  Objective, format, channel specs, messaging, CTA, brand guidelines
      │
CONCEPT & COPY
  Messaging hierarchy, value proposition, headline variations
      │
DESIGN & PRODUCTION
  Visual design, animation, video editing, HTML5 build
      │
INTERNAL REVIEW
  Brand review, legal/compliance check, stakeholder approval
      │
PLATFORM QA
  Spec validation (size, duration, format), preview across devices
      │
TRAFFICKING
  Upload to ad server, assign to campaigns, tag generation
      │
LIVE MONITORING
  Impression delivery, render errors, click tracking
      │
PERFORMANCE ANALYSIS
  CTR, VCR, engagement — by creative variant
      │
OPTIMIZATION
  Pause underperformers, scale winners, refresh creatives
      │
ARCHIVAL
  Expired creatives tagged, assets stored for future reuse
```

---

## 3. Creative Taxonomy

### By Format

#### Display / Banner
| IAB Standard Size | Name |
|-------------------|------|
| 300×250 | Medium Rectangle |
| 728×90 | Leaderboard |
| 160×600 | Wide Skyscraper |
| 320×50 | Mobile Banner |
| 300×600 | Half Page |
| 970×250 | Billboard |
| 320×480 | Mobile Interstitial |

#### Video
| Type | Description |
|------|-------------|
| Pre-roll | Before content, skippable at 5s |
| Non-skippable | 15-30s forced view |
| Bumper | 6s non-skippable |
| Mid-roll | During long-form content |
| Out-stream | Auto-plays in article feed |
| Rewarded | User opts in for reward |

#### Rich Media
- Expandable banners
- Video in banner
- Interactive (games, quizzes)
- 3D / AR ads

#### Native
- Headline + description + image + CTA
- Must match publisher page look-and-feel

#### CTV / OTT
- 15s, 30s, 60s video
- Interactive overlays (some platforms)

---

## 4. Creative Components (Anatomy of an Ad)

```
Visual Layer
  ├── Hero image / video
  ├── Logo
  ├── Background
  └── Animation / motion

Copy Layer
  ├── Headline (primary message)
  ├── Sub-headline (supporting message)
  ├── Body copy
  └── Disclaimer / legal text

Action Layer
  ├── CTA button (text + color + placement)
  └── Click destination URL

Brand Layer
  ├── Color palette
  ├── Typography
  ├── Brand voice
  └── Tagline
```

---

## 5. Creative Specifications Management

Each platform has unique specs. A Creative Management Platform (CMP) must handle:

| Spec Type | Examples |
|-----------|---------|
| File format | JPG, PNG, GIF, HTML5 ZIP, MP4, WebM |
| File size limit | 150KB (Google Display), 30MB (video) |
| Dimensions | Platform-specific pixel dimensions |
| Duration | Max 30s (video), max 3s (GIF) |
| Animation loops | Max 3 loops (IAB standard) |
| Safe area | Text must stay in center 80% of frame |
| Frame rate | 24fps, 30fps |
| Bitrate | Varies by platform |
| Audio | LUFS normalization, stereo |
| Click tag | Must be present for HTML5 |

---

## 6. Dynamic Creative Optimization (DCO)

DCO is the **automated assembly of personalized ads** from modular components at the moment of ad serving.

### DCO Architecture
```
DATA INPUTS (signals)
  ├── Audience segment
  ├── Location
  ├── Weather
  ├── Time of day
  ├── Device type
  ├── Behavioral data
  └── CRM / product feed

         ▼

DECISION ENGINE
  (Rules engine or ML model)
  Selects best combination of:
  ├── Background image
  ├── Headline
  ├── Offer / price (from product feed)
  ├── CTA text
  └── Language / locale

         ▼

CREATIVE ASSEMBLY
  HTML5 template + selected components

         ▼

RENDERED AD
  Unique to each user/context
```

### DCO Use Cases
- **Retail** — show the product a user browsed + current price
- **Travel** — destination images based on user's searched routes
- **Automotive** — model variant based on audience profile
- **Finance** — personalized rate/offer per segment

---

## 7. Creative Versioning & Variants

### Version Dimensions
| Dimension | Variants |
|-----------|---------|
| Format/Size | 300×250, 728×90, 320×50 |
| Language | EN, FR, DE, ES |
| Audience | New vs. Returning, B2B vs. B2C |
| Channel | Display, Social, Video |
| Message | Offer A vs. Offer B |
| CTA | "Buy Now" vs. "Learn More" |
| Season/Promotion | Holiday, Black Friday, Back to School |

### Naming Convention (Best Practice)
```
{Brand}_{Campaign}_{Format}_{Size}_{Version}_{Date}
Example: Nike_RunFast_Display_300x250_V3_2024Q4
```

---

## 8. Creative Approval Workflow

```
CREATOR submits draft
      │
INTERNAL BRAND REVIEW
  Brand compliance, visual guidelines
      │
LEGAL / COMPLIANCE REVIEW
  Claims validation, disclaimer check
      │
STAKEHOLDER SIGN-OFF
  Marketing manager / brand owner approval
      │
PLATFORM POLICY REVIEW
  Automated + manual review by platform (Google, Meta, etc.)
      │
QA / TRAFFICKING
  Spec check, preview, tag testing
      │
APPROVED → LIVE
```

### Approval States
| State | Description |
|-------|-------------|
| Draft | Work in progress |
| In Review | Submitted for approval |
| Changes Requested | Reviewer flagged issues |
| Approved (Internal) | Brand/legal cleared |
| Approved (Platform) | Ad platform cleared |
| Rejected | Does not meet policy |
| Expired | Past campaign end date |
| Archived | Stored for future reference |

---

## 9. Asset Management

### Creative Asset Types
- Source files (PSD, AI, AEP, FCP)
- Exported assets (PNG, JPG, MP4, HTML5)
- Fonts, logos, brand kit files
- Copy documents (scripts, headlines, body copy)
- Brand guidelines PDFs

### DAM (Digital Asset Management) Features
- Centralized storage
- Tagging / metadata
- Search and filtering
- Version control
- Rights management (license expiry)
- CDN delivery for fast serving
- Thumbnail previews

---

## 10. Creative Performance Analysis

### Metrics by Format

#### Display
- CTR, Impressions, Unique Reach, Frequency
- Interaction Rate (for rich media)
- Time-on-Ad

#### Video
- Views, VCR (Video Completion Rate)
- 25%, 50%, 75%, 100% completion milestones
- CPV, View-Through Rate
- Unmute rate (audio engagement)

#### Social
- Saves, Shares, Comments, Reactions
- Profile visits from ad
- Swipe-ups (Stories)

### Creative Fatigue Detection
```
Signal: CTR declining + Frequency rising
Threshold: CTR drops >30% from baseline at frequency > 4
Action: Refresh creative with new variant
```

---

## 11. Creative Testing Frameworks

### A/B Testing
- One variable changed per test
- Equal traffic split
- Statistical significance required (95% confidence)
- Min. sample: 100 conversions per variant recommended

### Multivariate Testing (MVT)
- Multiple elements tested simultaneously
- Requires larger sample sizes
- Good for landing pages, less practical for ads

### Sequential Testing
- Version A → Version B (before/after)
- Confounded by time effects; less rigorous

### DCO-Driven Testing
- Algorithmic optimization across many variants
- Platform's ML picks winners dynamically

---

## 12. Brand Safety in Creatives

- **Platform policies** — prohibited content categories
- **Ad verification** — IAS, DoubleVerify, MOAT
- **Contextual blocking** — avoid sensitive content categories
- **Creative scanning** — automated checks for policy violations before upload
- **Trademark protection** — logos not altered, claims accurate

---

## 13. Creative Management Platform (CMP) — Core Features

A dedicated CMP provides:

| Feature | Description |
|---------|-------------|
| Template Studio | Drag-and-drop HTML5 ad builder |
| Asset Library (DAM) | Centralized storage with metadata |
| Approval Workflow | Role-based multi-step sign-off |
| Spec Manager | Per-platform format validation |
| Versioning | Track all creative iterations |
| Distribution | One-click export to ad servers/platforms |
| Performance Dashboard | Creative-level analytics |
| DCO Engine | Dynamic personalization |
| Collaboration | Comments, annotations on creatives |
| Brand Governance | Enforce brand guidelines at scale |
