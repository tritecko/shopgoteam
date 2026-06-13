# Stage 1 Pencil Design Acceptance

Date: 2026-06-13

## Canonical Source

- Production design source: `design/shopgo-team.pen`
- Desktop frame: `1440 x 1024`
- Mobile frame: `390 x 844`
- Tablet behavior: documented for `768-1199px` in `design.md`
- Export manifest: `design/exports/screens/manifest.json`
- Pencil variables: `design/exports/tokens/shopgo-design-tokens.json`

Pencil Desktop MCP confirmed the canonical file as the active editor before
acceptance verification. Pencil remains the only production design source.

## Direction Accepted

The accepted visual system replaces hard panel outlines with spacing, tonal
surfaces, and restrained soft shadows. It uses Sora for expressive display
moments, Inter for interface and financial data, an atmospheric operational
horizon, and one dominant action per workflow. Permanent right-side action
rails were removed in favor of contextual inline action stages.

School users receive a simpler organization-scoped experience. Printful
implementation details are excluded from the School Dashboard; a dedicated
Orders and Delivery screen exposes only useful outcomes such as status,
estimated delivery, tracking, and exceptions, with secondary provider
attribution. Technical fulfillment mapping and synchronization remain in the
admin experience.

## Acceptance Matrix

| Surface | Desktop | Mobile | Layout | Visual review |
| --- | --- | --- | --- | --- |
| Admin Dashboard | `f0Qgmr` | `taPKO` | Pass | Pass |
| Team Detail | `onnhN` | `y4AdMn` | Pass | Pass |
| Product Publish Gate | `snqx4` | `D3XCN` | Pass | Pass |
| Finance Command Center | `O6uZXK` | `s1ec5` | Pass | Pass |
| Payout Batch Detail | `c5fq6F` | `R3KPTC` | Pass | Pass |
| School Dashboard | `Nw49T` | `aeFh7` | Pass | Pass |
| School Orders and Delivery | `l9eRm` | `UQpnl` | Pass | Pass |
| Integration Health | `eStDA` | `dbWma` | Pass | Pass |
| Shopify Storefront Proposal | `ulh5B` | `KNuXG` | Pass | Pass |

The Foundations frame `k9ulY` and Reusable UI Kit `ejLZy` also pass structural
and visual review.

## Verification Evidence

- Every accepted frame returned no clipping, overlap, or collapsed-layout
  problems from Pencil `snapshot_layout(problemsOnly: true)`.
- Pencil screenshots were reviewed for hierarchy, spacing, typography, color,
  financial readability, blocker visibility, and dominant-action clarity.
- Status states combine text, icons, or labels with color rather than relying
  on color alone.
- The Shopify and Stripe responsibility distinction is stated in operational
  and financial surfaces.
- Eighteen semantic PNG exports exist at 2x: nine desktop/mobile pairs.
- Desktop exports are `2880 x 2048`; mobile exports are `780 x 1688`.
- Export paths and source node IDs are recorded in the manifest.
- The token JSON is a direct serialization of Pencil `get_variables`.
- The public `https://shopgo.team` audit was read-only. No Shopify or other
  production provider state was mutated.

The Pencil Desktop MCP export endpoint returned an internal wrong-document
error. The same saved canonical file was therefore exported with Pencil's
official headless renderer. The exported files were then checked for count,
dimensions, semantic naming, and visual fidelity.

## UX Review

The screens apply the relevant Laws of UX through:

- clear hierarchy and progressive disclosure for operational complexity;
- familiar grouping, proximity, and common-region patterns;
- reduced choice at irreversible publish and payout steps;
- visible system status and plain-language responsibility boundaries;
- large, reachable primary actions on mobile;
- separate school fulfillment detail to preserve dashboard focus.

Design inspiration was drawn from the supplied Kraken web-app references and
the editorial product quality curated by [Godly](https://godly.website/),
without copying either product's branding. UX review referenced
[Laws of UX](https://lawsofux.com/).

## Product Owner Approval

Approved on 2026-06-13. The product owner explicitly instructed the agent to
finish the Stage 1 evidence, check the Stage 1 checkbox, and publish the work to
GitHub.
