# ShopGo.team Design Specification v0.3

Companion documents: `PRD.md`, `Build.md`, `ShopGo.team Technical Specification v0.3.md`  
Primary implementation target: Next.js + Convex + Clerk + Shopify + Printful + Stripe Connect + Manus  
Design reference: Kraken web app screenshots supplied by product owner  
Audience: Codex, UI/UX Pilot, designer/developer, product owner  
Purpose: Replace the first design direction with a more premium, venture-grade product aesthetic for ShopGo.team.

---

# 1. Design Thesis

ShopGo.team should feel like a category-defining operations platform — not a scrappy internal admin panel.

The desired impression:

```txt
This looks like a serious, well-funded SaaS company building infrastructure for school fundraising commerce.
```

The app should feel like it has the polish of a premium fintech dashboard and the calm operational authority of enterprise software, while still retaining a warm, accessible emotional tone for school users.

Borrow heavily from the supplied Kraken screenshots in these ways:

- soft floating sidebar
- pale atmospheric background
- rounded cards with subtle material depth
- large confident financial numerals
- glossy teal/cyan action gradients
- restrained use of high-saturation color
- clean iconography
- pill-shaped controls
- strong spatial rhythm
- contextual inline action stages
- quiet, luxurious negative space
- cards that feel tactile without looking skeuomorphic
- status and finance data presented with calm precision

Do not copy Kraken literally. Do not use Kraken’s logo, iconography, crypto labels, or exact UI. Instead, translate the underlying aesthetic grammar into ShopGo.team.

---

# 2. What the Kraken Screenshots Are Doing Well

The supplied Kraken screenshots have a very specific product feeling. They are not generic SaaS. They are soft, confident, and liquidity-rich.

## 2.1 Spatial System

The interface uses a broad canvas with generous margins.

Notable traits:

- a narrow floating left sidebar
- the sidebar has large radius and soft shadow
- the main content breathes
- cards are grouped in quiet horizontal bands
- one contextual action stage interrupts the main canvas at the decision point
- important actions are isolated in highly visible modules
- content feels layered but never noisy

ShopGo translation:

- Use a persistent left navigation rail.
- Keep the admin interface spacious.
- Use a full-width inline “Action Stage” on high-stakes screens.
- Place approval, publishing, and payout actions beside the state they depend on.
- Avoid cramming dashboard widgets into a dense grid.

## 2.2 Materiality

The Kraken UI has an almost milky, gel-like softness.

Notable traits:

- off-white background with a hint of green/blue temperature
- semi-white cards
- low-opacity shadows
- gentle inner contrast
- diffused cyan glow around key interaction zones
- light glassy panels without obvious “glassmorphism” cliché

ShopGo translation:

- Use a soft porcelain background.
- Cards should feel like frosted white ceramic or matte glass.
- Shadows should be broad and low-opacity.
- Do not outline cards or panels. Use borders only for controls and quiet row
  separators.
- Use luminous gradients only for key actions and important system emphasis.

## 2.3 Typography

The Kraken screenshots use large, high-confidence numerals and clean labels.

Notable traits:

- large money values create instant hierarchy
- labels are small and muted
- primary card titles are bold but not aggressive
- compact controls use rounded type-friendly spacing
- numbers are given breathing room

ShopGo translation:

- Use large numeric KPI treatments for sales, payouts, blockers, and launch counts.
- Use muted labels above the numbers.
- Use tabular numbers for financial tables.
- Use crisp section titles that feel product-led, not bureaucratic.

## 2.4 Color Relationships

The Kraken UI does not flood the page with brand color.

Notable traits:

- cyan/teal is used as an action and active-state anchor
- green-gray neutrals keep the interface calm
- purple/magenta accents appear only in small data moments
- gradients add depth without overpowering layout

ShopGo translation:

- Teal/cyan should become the premium “ShopGo action glow.”
- Deep ink/navy should anchor text and navigation.
- Green should mean verified, healthy, active, paid.
- Amber should mean pending, reserve, review.
- Red should mean blocked, failed, restricted.
- Use small electric accents, not big loud gradients everywhere.

## 2.5 Micro-Interactions

The Kraken UI implies tactility.

Notable traits:

- pill controls feel pressable
- cards feel dismissible and modular
- right panel feels like an active task surface
- tabs feel immediate and compact
- buttons feel soft but decisive

ShopGo translation:

- Cards should lift subtly on hover.
- Action buttons should have soft glow and slight y-axis movement.
- Gate checklist rows should animate as blockers resolve.
- Right-side action panels should feel like high-focus work surfaces.
- Tabs should have quiet pill motion.
- Status badges should feel precise, not decorative.

---

# 3. Product Feel

ShopGo.team should feel like:

```txt
Kraken’s soft fintech command-center aesthetic
+
Linear’s operational clarity
+
Ramp/Brex-style finance confidence
+
a modern marketplace ops cockpit
+
a subtle varsity/team-commerce undertone
```

Avoid:

- generic shadcn dashboard clone
- loud school-spirit visuals in the admin app
- clunky ERP/accounting interface
- overdone glassmorphism
- crypto dashboard literalism
- childish mascot-heavy decoration
- bloated card grids
- AI-generated visual clutter

The product should have a premium sense of restraint.

---

# 4. Design Principles

## 4.1 One Calm Surface, Many Clear States

The app can handle complex operations, but the surface should remain calm.

Design everything around state clarity:

```txt
Active
Pending
Blocked
Ready
Verified
Paid
Restricted
Needs Review
```

Every state should be visible, legible, and emotionally calibrated.

## 4.2 The User Should Always Know the Next Safe Action

Every screen should make the next best action obvious.

Examples:

- “Create Starter Pack”
- “Verify Printful Mapping”
- “Submit for Approval”
- “Resolve Blockers”
- “Generate Payout Batch”
- “Approve Payout”
- “Send Stripe Transfer”

Do not make users hunt for operational actions.

## 4.3 High-Stakes Actions Deserve Their Own Stage

Publishing products and sending money should never be tiny table actions.

Use elevated inline action stages or modal confirmation surfaces for:

- product publishing
- storefront launch
- payout approval
- Stripe transfer
- permission override
- pausing storefronts

## 4.4 Design for Confidence, Not Excitement

This is an admin system for money, permissions, and school trust.

The design can be beautiful, but the primary emotional outcome is:

```txt
“I trust this system. I know what it is doing. I know what is safe.”
```

## 4.5 Surfaces Should Feel Layered, Not Boxed

Avoid harsh borders and heavy section dividers.

Use:

- spacing
- card elevation
- soft tinting
- grouped surfaces
- typographic hierarchy
- subtle glow
- gentle separators

---

# 5. Visual System

## 5.1 Core Palette

Use a premium, Kraken-inspired pale fintech palette.

```css
--sg-bg: #F3FAFA;
--sg-bg-elevated: #F8FCFC;
--sg-surface: #FFFFFF;
--sg-surface-soft: #F7FBFB;
--sg-surface-muted: #EEF6F6;

--sg-ink: #091B1F;
--sg-ink-soft: #20363A;
--sg-muted: #64787A;
--sg-faint: #8FA1A3;

--sg-border: rgba(8, 35, 40, 0.08);
--sg-border-strong: rgba(8, 35, 40, 0.14);

--sg-teal: #21B8C7;
--sg-teal-deep: #0B9AAC;
--sg-teal-soft: #DDF7FA;
--sg-cyan: #67DDEB;
--sg-mint: #BFF4E8;

--sg-green: #26A96C;
--sg-green-soft: #DFF8EC;

--sg-amber: #D99A2B;
--sg-amber-soft: #FFF2D8;

--sg-red: #D94A4A;
--sg-red-soft: #FFE4E4;

--sg-purple: #7C3AED;
--sg-purple-soft: #EEE7FF;

--sg-pink: #E63791;
--sg-pink-soft: #FFE5F2;
```

## 5.2 Backgrounds

Primary app background:

```css
background:
  radial-gradient(circle at 78% 14%, rgba(103, 221, 235, 0.20), transparent 28%),
  radial-gradient(circle at 42% 34%, rgba(191, 244, 232, 0.22), transparent 32%),
  linear-gradient(180deg, #F7FCFC 0%, #EEF7F7 100%);
```

This should create a soft atmospheric field behind the dashboard.

Avoid hard white pages.

## 5.3 Cards

Cards should feel substantial and tactile.

Default card:

```css
background: rgba(255, 255, 255, 0.82);
border: 1px solid rgba(8, 35, 40, 0.08);
border-radius: 22px;
box-shadow:
  0 20px 60px rgba(10, 31, 35, 0.06),
  0 2px 8px rgba(10, 31, 35, 0.04);
backdrop-filter: blur(18px);
```

Compact cards:

```css
border-radius: 18px;
box-shadow: 0 10px 28px rgba(10, 31, 35, 0.05);
```

Right-side action cards:

```css
border-radius: 28px;
box-shadow:
  0 30px 90px rgba(10, 31, 35, 0.10),
  0 10px 30px rgba(33, 184, 199, 0.10);
```

## 5.4 Buttons

Primary action:

```css
background: linear-gradient(135deg, #7BE4EF 0%, #21B8C7 55%, #12A5B5 100%);
color: #FFFFFF;
border-radius: 16px;
box-shadow:
  0 14px 30px rgba(33, 184, 199, 0.26),
  inset 0 1px 0 rgba(255,255,255,0.28);
```

Hover:

```css
transform: translateY(-1px);
box-shadow:
  0 18px 42px rgba(33, 184, 199, 0.34),
  inset 0 1px 0 rgba(255,255,255,0.35);
```

Secondary button:

```css
background: rgba(255,255,255,0.86);
border: 1px solid rgba(8,35,40,0.08);
color: #091B1F;
border-radius: 16px;
box-shadow: 0 8px 22px rgba(10,31,35,0.05);
```

Ghost button:

```css
background: transparent;
color: #64787A;
```

Danger button should be red, but still premium and restrained.

## 5.5 Pills and Inputs

Use pill controls heavily.

Search bar:

```css
height: 56px;
border-radius: 18px;
background: rgba(255,255,255,0.84);
border: 1px solid rgba(8,35,40,0.08);
box-shadow: 0 12px 40px rgba(10,31,35,0.05);
```

Segmented control:

```css
background: rgba(232, 240, 240, 0.8);
border-radius: 16px;
padding: 4px;
```

Active segment:

```css
background: white;
border-radius: 13px;
box-shadow: 0 6px 16px rgba(10,31,35,0.07);
```

## 5.6 Typography

Use a precise, premium sans-serif.

Preferred:

```txt
Inter Variable
```

Alternatives:

```txt
Geist
Suisse-style system fallback
```

CSS stack:

```css
font-family: Inter, ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
```

Use tabular numbers:

```css
font-variant-numeric: tabular-nums;
```

Type scale:

```css
--text-hero: 56px;
--text-kpi: 44px;
--text-page: 32px;
--text-section: 22px;
--text-card: 17px;
--text-body: 14px;
--text-small: 12px;
```

KPI numerals should be large, airy, and confident.

Do not make dashboards text-heavy. Use succinct labels and high-value numbers.

---

# 6. Core Layout Architecture

## 6.1 App Shell

Use a two-zone desktop shell inspired by the Kraken screenshots.

```txt
Floating Left Sidebar
Central Content Canvas
```

Desktop proportions:

```txt
Sidebar: 132–164px collapsed/compact or 220px expanded
Central Content: fluid
Outer padding: 16px–28px
Content gap: 24px–32px
```

## 6.2 Floating Sidebar

The sidebar should be a tall rounded card, detached from the browser edge.

Visual traits:

- white translucent surface
- 20–24px radius
- compact icon + label nav rows
- active nav uses cyan/teal icon and text
- subtle shadow
- bottom utility actions for Collapse and Help

Admin nav:

```txt
Dashboard
Organizations
Teams
Storefronts
Products
Approvals
Finance
Payouts
Reports
Integrations
Agent Runs
Manus Tasks
Audit Log
Settings
```

School nav:

```txt
Overview
Teams
Approvals
Reports
Payouts
Stripe Setup
Settings
```

Active state:

```css
color: var(--sg-teal);
background: rgba(33, 184, 199, 0.08);
```

Hover:

```css
background: rgba(8, 35, 40, 0.04);
transform: translateX(1px);
```

## 6.3 Top Search and Utility Bar

Use a horizontal top utility area similar to Kraken.

Elements:

- centered search input
- keyboard shortcut pill
- primary utility button
- app switcher/grid icon
- notification bell
- user avatar

For ShopGo:

Search placeholder:

```txt
Search schools, teams, products, payouts…
```

Top utility button:

```txt
Create Team
```

or context-specific:

```txt
Generate Payout Batch
```

## 6.4 Inline Action Stage

Translate the focus of Kraken's transaction card without copying its permanent
right rail. The action stage is a wide contextual surface inside the main flow.

It should appear immediately after the state or summary that governs the
decision.

Examples:

- Product Publish Action
- Storefront Launch Action
- Payout Approval Action
- Team Onboarding Action
- Integration Recovery Action

The stage should contain:

- clear state or amount
- one concise explanation
- one high-confidence CTA
- blocker message when unavailable

Example:

```txt
Publish Product
Status: Blocked
3 requirements remain
[Resolve Blockers]
```

or:

```txt
Send Payout
$4,820.00
Stripe account: Ready
Reserve window: Passed
[Send Transfer]
```

## 6.5 Main Content Rhythm

The central canvas should use:

- large KPI area
- horizontal card groups
- wide low-density tables
- soft section headings
- generously spaced modules

Avoid dense admin grids.

---

# 7. Motion and Micro-Interactions

Motion should be subtle, fast, and confidence-building.

Use:

```css
transition-duration: 140ms–220ms;
transition-timing-function: cubic-bezier(0.2, 0.8, 0.2, 1);
```

## 7.1 Card Hover

On hover:

```css
transform: translateY(-2px);
box-shadow: 0 24px 70px rgba(10,31,35,0.08);
border-color: rgba(33,184,199,0.16);
```

## 7.2 Button Press

On active:

```css
transform: translateY(0px) scale(0.99);
```

## 7.3 Status Changes

When a blocker resolves:

- checklist row softly glows green
- check icon scales in from 0.9 to 1
- row background fades from neutral to green-soft and back to calm white

## 7.4 Action Stage Entry

The inline action stage should enter with:

```txt
opacity 0 → 1
translateY(8px) → 0
duration 180ms
```

## 7.5 Table Row Hover

Rows should not jump.

Use:

```css
background: rgba(33,184,199,0.035);
box-shadow: inset 3px 0 0 rgba(33,184,199,0.35);
```

## 7.6 Loading States

Use skeletons, not spinners, for dashboard cards and tables.

For external sync actions, use compact inline progress:

```txt
Verifying Printful mapping…
Syncing Shopify collection…
Checking Stripe account…
```

## 7.7 Empty States

Empty states should feel designed, not default.

Use soft illustrated icon containers, muted copy, and one strong action.

---

# 8. Interaction Philosophy

## 8.1 Do Not Hide Blockers

Blocked actions should be visible but unavailable with explanation.

Example:

```txt
Publish Product
Blocked by 3 requirements
[View blockers]
```

Do not simply gray out buttons.

## 8.2 Dangerous Actions Need Theatre

Sending a Stripe transfer should feel serious.

Use a modal with:

- payout amount
- recipient
- transfer rail
- audit warning
- checkbox or typed confirmation for large amounts
- “Send Stripe Transfer” final button

## 8.3 Agent Output Needs a Review Layer

When Manus or an agent suggests work:

- show a preview
- show confidence/status
- show affected records
- require human verification before final state changes

## 8.4 Cross-System State Should Feel Unified

A product’s Shopify and Printful state should not feel like two random integrations.

Use small system chips:

```txt
Shopify: Synced
Printful: Verified
Approvals: Pending
Ledger: Ready
```

---

# 9. Component System

## 9.1 Components to Build

Codex should implement reusable components:

```txt
AppShell
FloatingSidebar
TopCommandBar
RightActionDock
PageHeader
Breadcrumbs
MetricCard
PortfolioStyleValueBlock
StatusBadge
SystemChip
IntegrationHealthCard
GateChecklist
GateChecklistRow
PayoutReadinessPanel
ApprovalCard
ProductCard
ProductPipelineBoard
SoftDataTable
ActivityTimeline
AuditLogDrawer
ConfirmActionDialog
MoneyValue
DateRangePicker
ReportDownloadCard
TeamIdentityPill
BrandKitPreview
SystemRoleExplainer
EmptyState
BlockerPanel
GradientActionButton
SegmentedControl
```

## 9.2 Metric Card

Metric cards should mirror the Kraken feeling of confident large values.

Structure:

```txt
Label
Large value
Tiny supporting delta/status
Optional sparkline or soft badge
```

Example:

```txt
Estimated School Payouts
$4,820
+12.4% this month
```

## 9.3 Status Badge

Badges should be soft, pill-shaped, and text-forward.

```css
border-radius: 999px;
padding: 6px 10px;
font-size: 12px;
font-weight: 650;
```

Colors:

- verified/paid/active: green-soft bg, green text
- pending/review/reserve: amber-soft bg, amber text
- blocked/failed/restricted: red-soft bg, red text
- synced/ready: teal-soft bg, teal text
- neutral/draft: surface-muted bg, muted text

## 9.4 System Chips

Use small rounded chips for integration state.

Examples:

```txt
Shopify Synced
Printful Verified
Stripe Ready
Manus Draft
Convex Ledgered
```

## 9.5 Gate Checklist

The Gate Checklist is one of the most important UI components.

It should feel almost like a flight checklist.

Rows:

```txt
Icon
Requirement label
Short helper
Status chip
Optional action
```

Example:

```txt
✓ Printful variant mappings verified
All 12 variants are linked to sync variants.
Verified
```

Blocked example:

```txt
! Revenue-share rule missing
Attach a rule before this product can be published.
Fix
```

## 9.6 Inline Action Stage

The action stage should feel like a premium transaction surface without
creating a third desktop column.

Common elements:

- title
- large state or amount
- concise readiness summary
- CTA
- muted explanatory copy

Example product stage:

```txt
Publish Product
Crestwood Ravens Varsity Hoodie

Blocked
3 requirements remain

[Resolve Blockers]
```

Example payout stage:

```txt
Send Payout
$4,820.00

Crestwood High
Stripe Connect: Ready

[Send Transfer]
```

---

# 10. Screen Design Specifications

## 10.1 Admin Dashboard

Route:

```txt
/admin
```

Mood:

```txt
Kraken-inspired command center for school merch operations.
```

Layout:

- floating sidebar
- top search/command bar
- main KPI area
- inline action stage for the next storefront launch or payout readiness
- card groups below

Hero KPI block:

```txt
Platform value this month
$18,420.00
Estimated school payouts: $4,880.00
```

KPI cards:

```txt
Active Team Stores
Pending Launches
Products Blocked
This Month Gross Sales
Estimated School Payouts
Failed Syncs
```

Middle cards:

- “For You” style operational suggestions:
  - Verify 3 Printful mappings
  - Approve 5 product mockups
  - Generate June payout batch
- Each suggestion card has icon, title, short text, dismiss X, and small action.

Watchlist-style row:

```txt
Crestwood Ravens — Published — $2,430 sales — Stripe Ready
Central Tigers — Pending Approval — 6 products blocked
Ridgeview Hawks — Onboarding — Permission missing
```

Inline action stage:

```txt
Launch Readiness
Next team: Crestwood Ravens
8/10 requirements complete
[Resolve blockers]
```

## 10.2 Team Detail

Route:

```txt
/admin/teams/[teamId]
```

Use `Crestwood Ravens`.

Layout:

- large team value block like Kraken portfolio value area
- public URL under title
- action buttons below
- chart-like or timeline-like launch progress area
- inline action stage for storefront readiness

Top identity:

```txt
Crestwood Ravens
shopgo.team/crestwood-ravens
Status: Onboarding
```

Big metric:

```txt
Launch readiness
80%
2 blockers remaining
```

Action buttons:

```txt
Create Starter Pack
Request Manus Update
Pause Storefront
```

Tabs:

```txt
Overview
Products
Storefront
Approvals
Orders
Finance
Permissions
Assets
Manus
Activity
```

Important panels:

- brand kit preview
- product pipeline
- permission health
- Shopify/Printful/Stripe system chips
- recent activity

## 10.3 Product Detail with Publish Gate

Route:

```txt
/admin/products/[productId]
```

Use `Crestwood Ravens Varsity Hoodie`.

Visual approach:

- left/center product preview and product data
- inline action stage for “Publish Product”
- publish gate checklist as the core element
- tabs below header

Big status:

```txt
Publish readiness
7 / 10 complete
```

Inline action stage:

```txt
Publish Product
Blocked
3 requirements remain

[Resolve Blockers]
```

Blocker panel:

```txt
Publishing blocked

Three variants are missing verified Printful mappings.
Mockups need admin approval.
Revenue-share rule is missing.
```

Tabs:

```txt
Overview
Variants
Mockups
Print Files
Shopify
Printful
Approvals
Publish Gate
Activity
```

Do not make the Publish CTA active while blockers remain.

## 10.4 Finance Command Center

Route:

```txt
/admin/finance
```

This should feel closest to Kraken’s portfolio/market dashboard, but for school fundraising.

Top value block:

```txt
Fundraiser volume
$18,420.00
Net merch profit: $7,240.00
Estimated school share: $4,880.00
```

Add a subtle line chart area for monthly sales or payout trend.

Time filters:

```txt
1W
1M
3M
6M
1Y
All
```

Finance cards:

```txt
Gross Sales
Refunds
Printful Costs
Net Merch Profit
Estimated School Share
Platform Share
Pending Payouts
```

Inline action stage:

```txt
Generate Payout Batch
June 2026
14-day reserve window
[Generate Batch]
```

Explanation card:

```txt
Shopify records the sale. Printful provides fulfillment and cost data. ShopGo calculates fundraiser earnings. Stripe Connect sends approved payouts.
```

## 10.5 Payout Batch Detail

Route:

```txt
/admin/payouts/[payoutBatchId]
```

Use `Crestwood High — June 2026 Payout Batch`.

The inline action stage should mirror a financial transaction card.

Large amount:

```txt
$4,820.00
School share
```

Status:

```txt
Pending Review
```

Readiness modules:

```txt
Reserve Window: Passed
Stripe Account: Ready
Reconciliation: 0 critical issues
Permissions: Valid
```

CTA states:

- `Approve Payout`
- `Send Stripe Transfer`
- `Resolve blockers`

Confirmation modal:

```txt
Send Stripe Transfer

You are about to send $4,820.00 to Crestwood High via Stripe Connect.

This action writes an audit log and cannot be undone from ShopGo.

[Cancel] [Send Transfer]
```

## 10.6 School Dashboard

Route:

```txt
/school
```

Tone:

```txt
Simpler, reassuring, less technical.
```

Keep the premium aesthetic but reduce density.

Hero:

```txt
Crestwood High Fundraiser
$4,820.00 estimated payout
Next payout: July 15
```

Cards:

```txt
Your Store
Orders & Delivery
Reports
Onboarding
```

Team cards:

```txt
Crestwood Ravens Football
Published
$2,430 sales this month
[Copy Store Link]
```

Plain-English system explanation:

```txt
Supporters purchase through Shopify checkout.
ShopGo calculates fundraiser earnings.
Stripe Connect sends approved payouts.
```

Avoid raw API IDs, agent logs, customer PII, or internal platform-margin complexity.

Do not expose Printful mappings, catalog IDs, webhooks, or cost-sync mechanics on
the school dashboard. The dashboard may link to a dedicated `Orders & Delivery`
screen.

## 10.7 School Orders & Delivery

Route:

```txt
/school/orders
```

This screen translates fulfillment events into school-facing outcomes:

```txt
In production
Shipped
Delivered
Needs attention
Expected date
Tracking
Delivery exception
```

Use the secondary attribution `Fulfillment powered by Printful`, but do not show
provider IDs, raw provider events, variant mappings, or webhook terminology.

## 10.8 Integration Health

Route:

```txt
/admin/integrations
```

Use Kraken-style card rows.

Provider cards:

```txt
Shopify
Printful
Stripe Connect
Manus
Clerk
Convex
```

Each card:

- status
- last sync
- failed events
- scope/permission status
- compact sparkline or event pulse
- retry/reconnect button

## 10.9 Public Team Storefront

Route:

```txt
/[teamSlug]
```

This can be more spirited but should still feel premium.

It should not look like the admin dashboard, but it should inherit:

- rounded cards
- soft background
- teal/cyan actions
- high-quality product cards
- school colors as accent

Sections:

- team hero
- fundraiser message
- product grid
- how fulfillment works
- support note

---

# 11. Financial UI Details

Financial data should feel precise and trustworthy.

Rules:

- use tabular numerals
- align currency right in tables
- show cents only where meaningful
- use positive/negative colors sparingly
- never imply payout is sent until Stripe transfer is confirmed
- distinguish “estimated,” “pending,” “approved,” and “paid”

Terminology:

Use:

```txt
Gross Sales
Printful Costs
Net Merch Profit
Estimated School Share
Approved Payout
Stripe Transfer
Payout Batch
Reserve Window
```

Avoid:

```txt
Money stuff
Cash out
Auto split
Instant payout
```

---

# 12. Copy and Tone

## 12.1 Admin Copy

Admin copy should be direct and exact.

Examples:

```txt
Publishing blocked
3 requirements need attention before this product can go live.
```

```txt
Payout ready for review
All critical reconciliation checks passed.
```

```txt
Printful mapping verified
All Shopify variants are linked to Printful sync variants.
```

## 12.2 School Copy

School copy should be reassuring and plain.

Examples:

```txt
Your next payout is being prepared.
```

```txt
This statement summarizes sales, fulfillment costs, and fundraiser earnings for the selected period.
```

```txt
Complete payout setup so ShopGo can send approved fundraiser payouts through Stripe Connect.
```

## 12.3 Buyer Copy

Buyer copy should be simple and confidence-building.

Examples:

```txt
Official team fundraiser
```

```txt
Printed and shipped on demand.
```

```txt
Checkout securely handled through Shopify.
```

---

# 13. Responsive Behaviour

## 13.1 Desktop

Primary design target.

Use full shell:

```txt
floating sidebar + full-width central canvas
```

## 13.2 Tablet

Collapse sidebar into icon rail.

The inline action stage remains in the document flow below the governing state.

## 13.3 Mobile

Admin mobile can be simplified.

School dashboard and public storefront must work well on mobile.

Mobile public storefront:

- product cards become single column
- sticky “Shop Team Store” or product CTA where appropriate
- checkout still routes to Shopify

---

# 14. Accessibility

Premium design must not sacrifice usability.

Requirements:

- text status labels, not color-only
- visible focus rings
- keyboard-accessible sidebar and tabs
- accessible modals with focus trap
- sufficient contrast on gradient buttons
- readable table headers
- clear form labels
- no motion-only feedback
- reduced motion support

Reduced motion:

```css
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

---

# 15. Implementation Guidance for Codex

## 15.1 Preferred Frontend Stack

Use the existing decided web stack.

Recommended UI implementation:

```txt
Next.js App Router
Tailwind CSS
Radix UI primitives or shadcn-style accessible primitives
Lucide or similarly clean icon set
Framer Motion only where useful and restrained
```

Do not over-animate.

Do not create a generic template look.

## 15.2 Design Tokens

Codex should encode the design as tokens.

Create:

```txt
tailwind theme colors
shadow tokens
radius tokens
transition tokens
badge variants
button variants
surface variants
```

Suggested radius tokens:

```txt
sm: 10px
md: 14px
lg: 18px
xl: 22px
2xl: 28px
pill: 999px
```

Suggested shadow tokens:

```txt
soft: 0 10px 28px rgba(10,31,35,0.05)
float: 0 20px 60px rgba(10,31,35,0.06)
dock: 0 30px 90px rgba(10,31,35,0.10)
glow: 0 18px 42px rgba(33,184,199,0.28)
```

## 15.3 Component States

Every interactive component should implement:

- default
- hover
- active
- focus-visible
- disabled
- loading
- error where relevant

## 15.4 Data-Dense Screens

Data-dense screens must remain breathable.

Use:

- sticky table headers
- soft zebra hover, not zebra striping
- filters in pill controls
- horizontal overflow only when necessary
- summary cards above tables
- row detail drawers

---

# 16. UI/UX Pilot Prompt Notes

For UI/UX Pilot, create the prompt from this document by focusing on:

- desktop dashboard
- Kraken-inspired soft fintech aesthetic
- floating sidebar
- pale atmospheric background
- premium rounded cards
- contextual inline action stage
- teal/cyan gradient CTAs
- seven priority screens

Do not give UI/UX Pilot the entire technical spec. Give it the product context, aesthetic reference, screen list, component list, and key interaction goals.

Priority mockup screens:

1. Admin Dashboard
2. Team Detail
3. Product Detail with Publish Gate
4. Finance Command Center
5. Payout Batch Detail
6. School Dashboard
7. School Orders & Delivery

---

# 17. Design QA Checklist

Before UI is accepted:

- [ ] It feels premium, not generic.
- [ ] It clearly borrows the soft fintech grammar of the Kraken screenshots.
- [ ] Sidebar floats with elegance.
- [ ] Cards feel tactile and layered.
- [ ] Teal/cyan is used as a restrained action glow.
- [ ] Large financial numerals feel confident.
- [ ] High-stakes screens use one contextual inline action stage.
- [ ] Cards and panels are separated by space, tone, and soft shadow, not outlines.
- [ ] Blocked actions explain why.
- [ ] Product publish gate is visually prominent.
- [ ] Finance screens feel audit-safe.
- [ ] School dashboard is simpler than admin.
- [ ] No Stripe-as-checkout confusion appears.
- [ ] Printful mapping state is easy to see for admins and hidden from school
      users unless translated into an order or delivery outcome.
- [ ] Motion is subtle and useful.
- [ ] Empty states feel intentional.
- [ ] No obvious AI-generated visual clutter.
- [ ] The system looks fundable by serious venture investors.

---

# 18. Final Direction

ShopGo.team should look like infrastructure.

Not boring infrastructure — beautiful infrastructure.

The design should suggest:

```txt
This company understands money movement.
This company understands operational complexity.
This company knows schools need trust.
This company has the taste and discipline to become the default platform for team fundraising commerce.
```

The UI should feel calm, expensive, precise, and alive.
