# Plugin Product Backlog — User Stories & Acceptance Criteria

## EPIC 1: Creative Asset Management

---

### US-001: Upload Creative Asset
**As a** Creative Producer,
**I want to** upload ad creative files (images, HTML5, video),
**So that** they are stored centrally and accessible to the team.

**Acceptance Criteria:**
- [ ] Supports file types: JPG, PNG, GIF, SVG, HTML5 (ZIP), MP4, WebM, MOV
- [ ] Auto-extracts metadata on upload: dimensions, file size, format, duration (video)
- [ ] Shows upload progress indicator
- [ ] Validates file against a pre-selected platform spec on upload
- [ ] Assigns version number (v1 on first upload)
- [ ] User can add name, description, and tags

**Story Points:** 5

---

### US-002: Version Creative Asset
**As a** Creative Producer,
**I want to** upload a new version of an existing asset,
**So that** I preserve history and the team always has the latest.

**Acceptance Criteria:**
- [ ] "Replace / New Version" option on any existing asset
- [ ] Previous versions remain accessible and downloadable
- [ ] Version number increments automatically
- [ ] Campaign assignments carry forward to the new version unless manually changed
- [ ] Changelog entry auto-created with uploader name and timestamp

**Story Points:** 3

---

### US-003: Search and Filter Assets
**As a** Campaign Manager,
**I want to** search and filter the asset library,
**So that** I can quickly find the creative I need for trafficking.

**Acceptance Criteria:**
- [ ] Full-text search on name, tags, description
- [ ] Filters: format, dimensions, approval status, date range, campaign, uploader
- [ ] Thumbnail previews in grid and list views
- [ ] Sort by: date uploaded, name, file size, status

**Story Points:** 3

---

### US-004: Preview Creative
**As a** Brand Manager,
**I want to** preview a creative in a simulated ad placement,
**So that** I can review it accurately before approving.

**Acceptance Criteria:**
- [ ] Preview shows the creative at actual dimensions
- [ ] Simulated page background (white, content page mockup)
- [ ] Video creatives are playable in preview
- [ ] HTML5 creatives are rendered and interactive
- [ ] Preview available on mobile and desktop viewports

**Story Points:** 5

---

### US-005: Spec Validation
**As a** Campaign Manager,
**I want to** validate a creative against a specific platform's specs,
**So that** I catch issues before trafficking and avoid rejected ads.

**Acceptance Criteria:**
- [ ] User selects platform (Google Display, Meta, DV360, etc.)
- [ ] System checks: file size, dimensions, format, animation rules, click tag (HTML5)
- [ ] Result shown as Pass / Warning / Fail per rule
- [ ] Specific error message and fix guidance for each failure
- [ ] Validation runs automatically on upload (with default platform selection)

**Story Points:** 8

---

## EPIC 2: Approval Workflow

---

### US-010: Configure Approval Workflow
**As an** Admin,
**I want to** configure a multi-step approval workflow,
**So that** every creative goes through the right review process before going live.

**Acceptance Criteria:**
- [ ] Admin can define steps (name, description, assigned role)
- [ ] Steps can be marked as optional or required
- [ ] Skip rules configurable (e.g., skip legal for non-claim creatives)
- [ ] Workflow can be assigned per client, per campaign type, or globally
- [ ] SLA per step configurable (hours before reminder, then escalation)

**Story Points:** 8

---

### US-011: Submit Creative for Approval
**As a** Creative Producer,
**I want to** submit a creative into the approval workflow,
**So that** reviewers are notified and can take action.

**Acceptance Criteria:**
- [ ] "Submit for Approval" button visible on asset detail view
- [ ] User selects applicable workflow (or system picks by rule)
- [ ] Optional note to reviewers
- [ ] Status changes to "Pending Review"
- [ ] Email notification sent to first reviewer
- [ ] Submitter sees real-time status in their task view

**Story Points:** 3

---

### US-012: Review and Approve Creative
**As a** Brand Manager,
**I want to** review a creative and approve or request changes,
**So that** only compliant ads go live.

**Acceptance Criteria:**
- [ ] Reviewer sees preview, metadata, spec validation result, and submitter notes
- [ ] Actions: Approve, Reject, Request Changes (with mandatory comment)
- [ ] On approval: next step reviewer is notified, or status moves to "Approved"
- [ ] On rejection: creator notified with comments, asset returns to Draft
- [ ] On request changes: specific comment required, asset returns to In Progress
- [ ] All actions logged in workflow history with timestamp

**Story Points:** 5

---

### US-013: Bulk Approve Variants
**As a** Brand Manager,
**I want to** approve multiple size variants of the same creative at once,
**So that** I don't have to review identical content in five different sizes individually.

**Acceptance Criteria:**
- [ ] "Approve All Variants" available when reviewing a creative that has linked variants
- [ ] User can see all variants in a grid before bulk approving
- [ ] Any variant with a spec failure is excluded from bulk action and flagged
- [ ] Bulk approval logged as separate entries per asset in audit trail

**Story Points:** 3

---

### US-014: Approval SLA Alert
**As an** Admin,
**I want** the system to send reminders when a review is overdue,
**So that** campaigns are not delayed by unanswered approvals.

**Acceptance Criteria:**
- [ ] Reminder email sent X hours after assignment (configurable)
- [ ] Escalation email sent to manager if not acted on after Y hours
- [ ] Dashboard shows overdue items highlighted in red
- [ ] Reporter (submitter) can see when SLA is at risk

**Story Points:** 3

---

## EPIC 3: Campaign Builder

---

### US-020: Create Campaign Structure
**As a** Campaign Manager,
**I want to** create a campaign with ad sets and targeting in the plugin,
**So that** I have a single source of truth before syncing to platforms.

**Acceptance Criteria:**
- [ ] Form fields: name, objective, start/end date, budget, budget type, pacing
- [ ] Add Ad Sets (name, audience, budget, schedule, bid strategy)
- [ ] Add Ads (creative assignment, destination URL, CTA)
- [ ] Save as Draft before syncing to any platform
- [ ] Validation on save: required fields, date conflicts, budget arithmetic

**Story Points:** 8

---

### US-021: Clone Campaign
**As a** Campaign Manager,
**I want to** clone an existing campaign structure,
**So that** I can quickly create a new campaign based on a proven setup.

**Acceptance Criteria:**
- [ ] Clone preserves: structure, targeting, budgets (editable after clone)
- [ ] Cloned creatives are linked, not duplicated
- [ ] Clone shows as a new Draft with "(Copy)" appended to name
- [ ] User prompted to update dates and budget before saving

**Story Points:** 3

---

### US-022: Campaign Template Library
**As a** Campaign Manager,
**I want to** save a campaign structure as a reusable template,
**So that** I can standardize repeatable campaign types across clients.

**Acceptance Criteria:**
- [ ] "Save as Template" from any campaign
- [ ] Template stores: structure, targeting config, format specs (no dates/budgets)
- [ ] Templates browsable and searchable in a library
- [ ] Templates can be private (creator-only) or shared (org-wide)
- [ ] "Create from Template" flow populates campaign with template settings

**Story Points:** 5

---

### US-023: Sync Campaign to Platform
**As a** Campaign Manager,
**I want to** push my campaign setup to Google Ads or Meta,
**So that** I don't have to re-enter it manually in each platform UI.

**Acceptance Criteria:**
- [ ] User selects target platform(s) for sync
- [ ] Pre-sync validation: required platform-specific fields filled
- [ ] Shows field mapping (plugin field → platform field) for user review
- [ ] Sync runs asynchronously with status indicator
- [ ] Platform campaign/ad IDs stored and linked in plugin
- [ ] Error details shown with actionable fix instructions if sync fails

**Story Points:** 13

---

## EPIC 4: Analytics & Reporting

---

### US-030: Cross-Platform Performance Dashboard
**As a** Performance Analyst,
**I want to** see campaign performance from all platforms in one view,
**So that** I can compare and optimize without switching between platform UIs.

**Acceptance Criteria:**
- [ ] Aggregated metrics: Impressions, Clicks, CTR, Spend, Conversions, CPA, ROAS
- [ ] Breakdown by: Platform, Campaign, Ad Set, Creative, Date
- [ ] Date range picker with presets (Today, Last 7d, Last 30d, Custom)
- [ ] Metric definitions consistent and labeled (differences from platform noted)
- [ ] Data refresh: at minimum daily (hourly if API allows)

**Story Points:** 13

---

### US-031: Creative Performance Report
**As a** Creative Producer,
**I want to** see which of my creative variants performs best,
**So that** I can make informed decisions on what to produce next.

**Acceptance Criteria:**
- [ ] Creative-level metrics: Impressions, CTR, VCR (video), CPA (if conversion data)
- [ ] Side-by-side comparison of variants
- [ ] Fatigue indicator: flags creatives where CTR has declined >30% week-over-week
- [ ] "Which campaigns use this creative" traceable from the report

**Story Points:** 8

---

## EPIC 5: Platform & Integration

---

### US-040: White-Label Theme Configuration
**As a** Platform Admin (integrating company),
**I want to** configure the plugin's visual theme,
**So that** it matches our product's design system and feels native.

**Acceptance Criteria:**
- [ ] Theme config accepts: primary color, font family, logo URL, border radius
- [ ] Configuration via initialization options (JS SDK or React props)
- [ ] Theme applied across all plugin UI components
- [ ] Defaults to a neutral, professional theme if not configured

**Story Points:** 5

---

### US-041: Webhook Events
**As a** Developer integrating the plugin,
**I want to** receive webhook notifications on key events,
**So that** my platform can react to changes (e.g., creative approved → auto-traffic).

**Acceptance Criteria:**
- [ ] Events: creative.approved, creative.rejected, campaign.launched, campaign.paused, sync.completed, sync.failed
- [ ] Webhook endpoint configurable in admin settings
- [ ] Payload includes event type, timestamp, entity IDs, and relevant metadata
- [ ] Retry on failure: 3 attempts with exponential backoff
- [ ] Webhook delivery log visible in admin panel

**Story Points:** 8

---

### US-042: API Key Management
**As an** Admin,
**I want to** manage API keys for the plugin's integration,
**So that** I can rotate credentials without downtime.

**Acceptance Criteria:**
- [ ] Create, revoke, and rotate API keys
- [ ] Each key shows: name, created date, last used, permissions scope
- [ ] Revoked keys immediately invalid (no grace period)
- [ ] API key usage logged (requests per day)

**Story Points:** 3
