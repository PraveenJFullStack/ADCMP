# AI Banner & Creative Generation — Module Design

## 1. Where It Fits in the Plugin

AI creative generation is NOT a separate product. It sits inside your **Creative Studio module**
as the *generation layer* — before assets enter the management/approval/distribution pipeline.

```
┌─────────────────────────────────────────────────────────────────┐
│                    CREATIVE STUDIO (Module 2)                   │
│                                                                 │
│  ┌─────────────────┐    ┌──────────────────────────────────┐   │
│  │  AI GENERATION  │───▶│     ASSET LIBRARY (DAM)          │   │
│  │  (new layer)    │    │  Upload → Tag → Version → Store  │   │
│  └─────────────────┘    └──────────────┬─────────────────── ┘  │
│                                        │                        │
│                         ┌─────────────▼──────────────────┐     │
│                         │     APPROVAL WORKFLOW           │     │
│                         └─────────────┬──────────────────-┘     │
│                                       │                         │
│                         ┌─────────────▼──────────────────┐     │
│                         │  SPEC VALIDATOR + DISTRIBUTION  │     │
│                         └────────────────────────────────-┘     │
└─────────────────────────────────────────────────────────────────┘

AI Generation feeds assets INTO the existing workflow —
so approval, versioning, trafficking all work the same way.
```

---

## 2. What "AI Banner Tool" Actually Means (Full Scope)

There are 4 distinct AI capabilities — each solves a different problem:

```
┌──────────────────────────────────────────────────────────────────┐
│  LAYER 1: AI COPY GENERATION                                     │
│  Generate headlines, body copy, CTA text from brief              │
│  Models: Claude, GPT-4o                                          │
├──────────────────────────────────────────────────────────────────┤
│  LAYER 2: AI IMAGE GENERATION                                    │
│  Generate background images, product scenes, lifestyle visuals   │
│  Models: DALL-E 3, Stable Diffusion, Ideogram, Flux              │
├──────────────────────────────────────────────────────────────────┤
│  LAYER 3: AI LAYOUT ASSEMBLY                                     │
│  Place image + copy + logo + CTA into a banner template          │
│  Tool: HTML5 template engine + AI placement logic                │
├──────────────────────────────────────────────────────────────────┤
│  LAYER 4: AI VARIANT SCALING                                     │
│  Auto-resize 1 master banner to all required ad sizes            │
│  Tool: Constraint-aware layout reflow                            │
└──────────────────────────────────────────────────────────────────┘
```

---

## 3. The AI Creative Generation Flow

```
BRIEF INPUT (plain text or structured form)
  "Nike running shoes. Offer: 30% off. Audience: male runners 25-44.
   Tone: energetic. CTA: Shop Now. Brand colors: black + neon green."

          │
          ▼
┌─────────────────────────────────────────────────────┐
│              AI ORCHESTRATION LAYER                 │
│                                                     │
│  Step 1: Parse brief → extract intent               │
│    - Product: Nike running shoes                    │
│    - Offer: 30% off                                 │
│    - Tone: energetic                                │
│    - CTA: Shop Now                                  │
│    - Colors: black, #39FF14 (neon green)            │
│                                                     │
│  Step 2: Generate copy variants (Claude/GPT)        │
│    H1: "Run Faster. Pay Less."                      │
│    H1: "30% Off. No Excuses."                       │
│    H1: "Your Next PR Starts Here."                  │
│                                                     │
│  Step 3: Generate / select image (DALL-E / Flux)    │
│    Prompt auto-built from brief                     │
│    → "Dynamic male runner, urban environment,       │
│       motion blur, dark background, neon accents"   │
│                                                     │
│  Step 4: Assemble layout (template engine)          │
│    Place: image + logo + headline + offer + CTA     │
│    Apply brand colors from brief/brand kit          │
│                                                     │
│  Step 5: Scale to all sizes                         │
│    300×250 / 728×90 / 160×600 / 320×50 / 300×600   │
└─────────────────────────────────────────────────────┘
          │
          ▼
CREATIVE VARIANTS GENERATED
  → Auto-saved to Asset Library
  → Spec validation auto-run
  → Submitted to Approval Workflow
  → Human reviews and approves/edits
```

---

## 4. AI Copy Generation — Detail

**Input:** Campaign brief (structured or free text)
**Output:** Multiple headline/body/CTA variants for A/B testing

### What Gets Generated
| Field | # Variants | Example |
|-------|-----------|---------|
| Headline | 3-5 | "Run Faster. Pay Less." |
| Sub-headline | 3 | "Nike Running — 30% off this week only" |
| Body copy | 2-3 | Short (15 words), medium (30 words) |
| CTA text | 3-5 | "Shop Now", "Claim Offer", "Get 30% Off" |
| Disclaimer | 1 | Auto-drafted, flags for legal review |

### Prompt Engineering (behind the scenes)
```
System: You are an expert ad copywriter. Write concise, high-converting ad copy.
        Follow these rules:
        - Headlines max 40 characters
        - Use active voice
        - Lead with benefit, not feature
        - Include the offer explicitly
        - Match the tone: {tone}

User: Product: {product}
      Offer: {offer}
      Target audience: {audience}
      Channels: {channels}
      Generate 5 headline variants, 3 sub-headline variants, 4 CTA variants.
      Return as JSON.
```

### Output Schema
```json
{
  "headlines": [
    { "text": "Run Faster. Pay Less.", "char_count": 21, "tone_score": 0.9 },
    { "text": "30% Off. No Excuses.",  "char_count": 20, "tone_score": 0.85 }
  ],
  "subheadlines": [
    { "text": "Nike Running — 30% off this week only", "char_count": 38 }
  ],
  "ctas": [
    { "text": "Shop Now",      "urgency": "low" },
    { "text": "Claim 30% Off", "urgency": "high" }
  ]
}
```

---

## 5. AI Image Generation — Detail

**Input:** Brief + brand colors + mood + product reference
**Output:** Background or hero images sized appropriately

### Providers (pluggable)
| Provider | Strength | API |
|----------|---------|-----|
| DALL-E 3 (OpenAI) | Photorealistic, instruction-following | OpenAI API |
| Stable Diffusion (via Replicate/FAL) | Customizable, fine-tunable on brand | Replicate API |
| Ideogram | Typography in images | Ideogram API |
| Flux (Black Forest Labs) | High quality, fast | FAL API |
| Adobe Firefly | Brand-safe, commercially cleared | Adobe API |

### Auto Prompt Builder
```typescript
function buildImagePrompt(brief: CampaignBrief): string {
  return [
    brief.product_description,
    `Mood: ${brief.tone}`,
    `Color palette: ${brief.brand_colors.join(', ')}`,
    `Setting: ${brief.environment || 'clean studio background'}`,
    `Style: professional advertising photography`,
    `Aspect ratio: ${getAspectRatioForFormat(brief.primary_format)}`,
    `Do not include text or logos`,  // text added by layout layer
    `Commercially safe, no copyrighted elements`
  ].join('. ');
}
```

### Important: Brand Asset Integration
- User can upload product images (packshots, lifestyle shots) as **reference images**
- AI uses them for style consistency (img2img, ControlNet, IP-Adapter techniques)
- Logo is NEVER AI-generated — always pulled from brand kit

---

## 6. AI Layout Assembly — Detail

This is the **hardest and most valuable piece**. It combines all elements into a pixel-perfect banner.

### Two Approaches

#### Approach A: Template-Based (Recommended for MVP)
```
Designer creates master HTML5 templates with named slots:
  - {hero_image}   — background image zone
  - {logo}         — fixed position from brand kit
  - {headline}     — typography zone with max-chars
  - {sub_headline} — secondary text zone
  - {offer_badge}  — optional overlay
  - {cta_button}   — button with configurable label + color

AI fills slots:
  → Selects/generates image → places in hero_image slot
  → Picks best headline variant → places in headline slot
  → Applies brand colors to CTA button
  → Renders final HTML5 banner
```

#### Approach B: Generative Layout (Advanced, Phase 3+)
```
AI generates the full layout from scratch:
  - No templates needed
  - AI decides placement, hierarchy, spacing
  - Uses vision model to check visual quality
  - Tools: AutoLayout models, Figma API, HTML5 renderer
```

---

## 7. Auto-Sizing / Format Scaling — Detail

This solves the biggest creative production pain: **making 1 ad in 10 sizes**.

```
MASTER DESIGN (e.g., 300×250)
  │
  ├── Constraint rules per size:
  │     - 728×90  Leaderboard  → headline only, CTA right-aligned
  │     - 160×600 Skyscraper   → stacked vertically, image crops top
  │     - 320×50  Mobile       → logo + CTA only (too small for body copy)
  │     - 300×600 Half Page    → full layout, larger image
  │     - 970×250 Billboard    → wide image, all elements horizontal
  │
  └── AI auto-adapts:
        - Image: smart crop (keep subject in frame)
        - Text: truncate to character limit per size
        - Elements: reposition to fit new canvas
        - Font size: scale proportionally with min/max bounds

Output: All sizes as separate assets, auto-named, auto-validated
```

### Smart Crop (for images)
```
Saliency detection identifies the subject (face, product, logo)
→ Crop always keeps subject in frame
→ For portrait → landscape: pan to subject, fill remaining with background extension
→ AI background extension (outpainting) fills empty space if needed
```

---

## 8. DCO Integration (AI + Dynamic Creative = Personalization at Scale)

AI generation + DCO is the full vision:

```
PRODUCT CATALOG (feed)
  │
  ├── For user who browsed Nike Air Max:
  │     AI → generates/selects hero image: Air Max
  │     AI → selects headline: "Still thinking about these?"
  │     Offer: current price from feed
  │     CTA: "Buy Now"
  │
  └── For user in "running" segment (never visited):
        AI → generates/selects hero image: runner lifestyle
        AI → selects headline: "Run Faster. Pay Less."
        Offer: "30% off all running shoes"
        CTA: "Shop Now"

Both ads assembled in real-time at impression time.
```

---

## 9. Brand Safety & Compliance Checks (AI-Powered)

AI generation must be guarded. Before any asset enters the approval workflow:

| Check | How |
|-------|-----|
| Logo integrity | Vision model verifies logo is unmodified |
| Color compliance | Pixel-sample dominant colors vs. brand palette |
| Claim detection | NLP scans copy for superlatives, claims requiring legal review |
| Image safety | Moderation API (OpenAI, AWS Rekognition) for NSFW content |
| Copyright check | Reverse image search / watermark detection |
| Competitor mention | NLP scans for competitor brand names |

Anything flagged → **blocked from approval workflow** with specific reason shown to user.

---

## 10. Competitive Landscape

| Tool | What It Does | Gap |
|------|-------------|-----|
| **AdCreative.ai** | AI generates banner images + copy | No campaign management, no workflow, no trafficking |
| **Pencil** | AI video + static ads, performance prediction | Video-focused, not embeddable, no DCO |
| **Smartly.io** | Creative automation + media buying | Enterprise-only, expensive, not a plugin |
| **Canva** | Design tool with some AI | Not ad-spec-aware, no campaign integration |
| **Bannerbear** | Programmatic banner generation via API | No AI generation, no copy, no campaign management |
| **Adobe Firefly** | AI image generation | No ad workflow, no campaign layer |
| **Your Plugin** | AI generation + asset management + approval + campaign + distribution + MCP | Full stack, embeddable, AI-native |

**Your edge:** No one connects AI generation → approval → campaign management → trafficking in a single embeddable plugin.

---

## 11. MVP Scoping — What to Build First

You do NOT need to build everything at once. Here's the lean sequence:

### Week 1-2: AI Copy Generation Only
- Input: free-text brief
- Output: headline/CTA variants shown in UI
- User picks favorites → saved as creative metadata
- No image generation yet
- **Value: immediate** — copywriters love it

### Week 3-4: Template-Based Layout Assembly
- 3 HTML5 master templates (simple, medium, premium)
- AI fills copy slots from generated copy
- User uploads their own image (no AI image gen yet)
- Output: 3-5 banner sizes auto-generated
- Runs through existing spec validator

### Week 5-6: AI Image Generation (1 provider: DALL-E 3)
- Brief → auto-prompt → image generated
- Image used in template assembly
- Regenerate button if not satisfied
- Manual image swap option

### Week 7-8: Auto-Sizing
- Smart crop for top 5 IAB sizes
- Human review step before saving all variants

### Phase 3+: DCO, Brand Safety AI, Generative Layout

---

## 12. API Design for AI Creative Module

```
POST /ai/generate-copy
Body: { brief: string, tone: string, channel: string, variants: number }
Returns: { headlines: [], subheadlines: [], ctas: [], body_copy: [] }

POST /ai/generate-image
Body: { prompt: string, style: string, dimensions: {w,h}, brand_colors: [] }
Returns: { image_url: string, prompt_used: string, generation_id: string }

POST /ai/assemble-banner
Body: { template_id: string, copy: {}, image_url: string, brand_kit_id: string }
Returns: { asset_id: string, preview_url: string, html5_url: string }

POST /ai/scale-to-sizes
Body: { master_asset_id: string, target_sizes: string[], smart_crop: boolean }
Returns: { assets: [{ size: "728x90", asset_id: string, preview_url: string }] }

POST /ai/check-compliance
Body: { asset_id: string, checks: ["logo", "colors", "claims", "safety"] }
Returns: { passed: boolean, flags: [{ check: string, issue: string, severity: string }] }
```
