# ShopGo.team Product Requirements Document

Version: 0.1  
Source Technical Spec: ShopGo.team Technical Specification v0.3  
Target Stack: Next.js + Convex + Clerk + Shopify + Printful + Stripe Connect + Manus  
Primary Build Agent: Codex  
Product Owner: TJ / ShopGo.team  
Document Purpose: Define the product, user workflows, MVP scope, sprint sequence, acceptance criteria, and implementation priorities for building ShopGo.team.

---

# 1. Executive Summary

ShopGo.team is a fundraising merch operations platform for high school sports teams.

The product enables ShopGo admins to create team-specific merch storefronts such as:

```txt
shopgo.team/crestwood-ravens
```

Supporters browse team-branded products and purchase through a Shopify checkout experience. Printful powers product sync, print-on-demand fulfillment, packing, and shipping. Stripe Connect powers post-sale school payouts after Convex calculates fundraiser earnings.

ShopGo.team is not merely a merch storefront builder. It is a school-safe, finance-friendly, admin-controlled fundraising operations system. Its core value is helping schools and teams launch merch fundraisers quickly while maintaining trust through approval workflows, clean reporting, verified fulfillment mappings, transparent payout calculations, and strong admin oversight.

The initial MVP must prioritize operational reliability over full automation. Agents and Manus may assist with setup, copy, storefront organization, and workflows, but human approval is required before products are published or payouts are sent.

---

# 2. Product Vision

ShopGo.team helps schools and sports teams launch professional merch fundraisers without needing to manage ecommerce, design production, fulfillment, payout tracking, or reporting complexity.

The platform gives ShopGo admins one command center for:

- creating schools and teams
- assigning team slugs
- uploading or generating brand assets
- managing school permission records
- creating Printful-backed product lineups
- syncing products into Shopify
- organizing products into team-specific collections/storefronts
- approving mockups and designs
- monitoring Shopify orders
- syncing Printful fulfillment and cost data
- calculating fundraiser earnings in Convex
- approving school payout batches
- sending post-sale payouts through Stripe Connect
- generating school finance reports

The school experience should feel simple and trustworthy:

> “Supporters buy the merch. ShopGo handles the store, fulfillment, payout calculation, and reports.”

---

# 3. Canonical Operating Model

Use this phrase consistently throughout UI copy, internal docs, and code comments:

```txt
Buyers purchase through a Shopify checkout experience.
Printful powers product sync, POD fulfillment, packing, and shipping.
Stripe Connect powers post-sale school payouts after Convex calculates fundraiser earnings.
```

System responsibility model:

| System | Responsibility |
|---|---|
| Next.js | Admin dashboard, school dashboard, optional team storefront renderer |
| Convex | Operating database, workflow state, ledger, reports, audit logs, backend functions |
| Clerk | Authentication, user identity, school/admin invitations |
| Shopify | Checkout, payment capture, product records, collections, customer orders |
| Printful | POD catalog, product sync, mockups, variant mappings, fulfillment, packing, shipping |
| Stripe Connect | Post-sale school/booster payout rail |
| Manus | Assisted Shopify/storefront/catalog operator |
| Agents | Workflow preparation, reconciliation, reporting, anomaly detection |

Important source-of-truth rules:

- Convex is the ShopGo operating system.
- Shopify is the checkout and order source of truth.
- Printful is the POD fulfillment source of truth.
- Stripe Connect is the post-sale payout rail.
- Manus is an operator, not an authority.
- Financial records must be ledger-based and immutable.

---

# 4. Product Goals

## 4.1 MVP Goals

The MVP must allow a ShopGo admin to:

1. Create a school/organization.
2. Create one or more teams.
3. Generate a team-specific slug.
4. Create a team storefront record.
5. Store school/team brand information.
6. Track permission status for school name, mascot, logo, colors, and fundraiser authorization.
7. Create product templates tied to Printful products.
8. Create team-specific products.
9. Track design, mockup, sync, approval, and publishing status.
10. Map Shopify products/variants to Printful sync products/variants.
11. Prevent publishing if Printful sync mapping is not verified.
12. Receive or simulate Shopify order data.
13. Create order and order item records.
14. Attach Printful cost/fulfillment data.
15. Generate immutable ledger entries.
16. Generate draft monthly payout batches.
17. Approve payout batches manually.
18. Track Stripe Connect account status and payout transfer IDs.
19. Generate school-facing payout/report records.
20. Maintain audit logs for sensitive operations.

## 4.2 Non-MVP Goals

The following are important but should not block the first working MVP:

- Fully autonomous AI-generated storefronts.
- Fully autonomous product publishing.
- Fully autonomous payout transfers.
- Advanced accounting integrations with QuickBooks/Xero/Sage.
- Multi-currency payout optimization.
- District-level bulk school onboarding.
- Buyer account system.
- Native checkout inside ShopGo.
- Replacing Shopify checkout.
- Replacing Printful fulfillment.
- Fully replacing Manus with custom storefront generation.

---

# 5. Success Metrics

## 5.1 Operational Metrics

- Time to create a new team storefront: under 20 minutes after assets are available.
- Percentage of published products with verified Printful mapping: 100%.
- Percentage of orders correctly attributed to a team: 99%+.
- Payout batches requiring manual ledger correction: below 5%.
- Products blocked from publishing due to missing permission/sync data: visible and explainable.

## 5.2 Business Metrics

- Number of active team storefronts.
- Gross merchandise sales by team.
- Net merch profit by team.
- School share paid out.
- Platform share retained.
- Repeat school/team launches.
- Average sales per team storefront.
- Time from onboarding to first sale.

## 5.3 Trust Metrics

- School finance admins can download monthly statements.
- Every payout is traceable to ledger entries.
- Every published product has approval records.
- Every school/mascot/logo usage has permission status.
- Every sensitive admin action has an audit log.

---

# 6. Personas

## 6.1 Super Admin

Primary user: ShopGo owner/operator.

Needs:

- Full control over schools, teams, products, approvals, storefronts, payouts, and integrations.
- Ability to pause/unpublish/freeze anything quickly.
- Clear status across Shopify, Printful, Stripe, Manus, and Convex.
- Confidence that no product goes live without required approvals and sync mappings.

Core actions:

- Create organization.
- Create team.
- Create storefront.
- Upload assets.
- Approve products.
- Publish products.
- Generate payout batches.
- Approve payouts.
- Review audit logs.
- Retry failed sync jobs.

## 6.2 ShopGo Operator

Future internal teammate or contractor.

Needs:

- Ability to prepare products/storefronts without being able to approve final payouts or change financial rules.
- Clear task queues.
- Clear error states.
- Guided setup workflows.

Core actions:

- Add school/team info.
- Prepare product drafts.
- Upload assets.
- Request mockups.
- Request Manus updates.
- Prepare payout batches.

## 6.3 School Financial Admin

Bookkeeper, treasurer, booster finance person.

Needs:

- Simple view of fundraiser earnings.
- Payout history.
- Downloadable statements.
- Clear explanation of how sales became payout amounts.
- Stripe onboarding access.
- No need to manage Shopify, Printful, or Stripe directly.

Core actions:

- View team sales.
- Download statements.
- Complete Stripe onboarding.
- View payout status.
- View yearly summary.

## 6.4 Athletic Director / Coach / Booster Contact

Needs:

- Approve designs and storefront presentation.
- Share public store link.
- See sales performance.
- Avoid finance complexity.

Core actions:

- Review product mockups.
- Approve or reject design.
- Copy public storefront link.
- View top-selling products.

## 6.5 Buyer

Needs:

- Simple team-specific merch shopping experience.
- Trustworthy checkout.
- Clear order confirmation.
- Fulfillment updates.

Core actions:

- Visit team page.
- Browse products.
- Purchase through Shopify checkout.

---

# 7. Primary User Journeys

## 7.1 Admin Creates a New Team Storefront

Preconditions:

- Admin is authenticated.
- Admin has `super_admin` or `operator` role.

Flow:

1. Admin opens dashboard.
2. Admin creates or selects an organization.
3. Admin creates a team.
4. System generates a unique team slug.
5. Admin confirms team details:
   - school name
   - mascot
   - sport
   - level
   - colors
   - location
6. System creates a `teamStorefront` record.
7. Admin uploads brand assets or continues with text-only brand information.
8. Admin creates or attaches permission records.
9. Team status becomes `onboarding`.

Acceptance criteria:

- A team cannot be created without organization, display name, and unique slug.
- Duplicate slugs are blocked.
- Team detail page shows storefront URL preview.
- Team status and permission status are visible.
- Audit log records creation.

---

## 7.2 Admin Creates Product Starter Pack

Preconditions:

- Team exists.
- Product templates exist.
- Brand kit exists or has enough data to proceed.

Flow:

1. Admin opens team detail page.
2. Admin clicks “Create Starter Pack.”
3. Admin selects templates:
   - tee
   - hoodie
   - crewneck
   - long sleeve
   - hat
4. System creates product draft records.
5. Products appear in product pipeline as `draft`.
6. Admin attaches design assets or initiates design brief workflow.
7. Products progress through design/mockup/sync states.

Acceptance criteria:

- Product records are associated with organization, team, storefront, and template.
- Products cannot skip required statuses.
- Each product has a visible status.
- Product pipeline displays all created drafts.
- Audit log records starter pack creation.

---

## 7.3 Product Sync and Publish Workflow

Preconditions:

- Product exists.
- Print file/design asset is attached.
- Product template maps to Printful catalog product.

Flow:

1. Admin creates or verifies Printful sync product.
2. System stores Printful sync product ID.
3. System pushes/syncs product to Shopify.
4. System stores Shopify product ID.
5. System creates/updates product variants.
6. System verifies Shopify variant ↔ Printful variant mapping.
7. Admin requests Printful mockups.
8. Mockups are attached to product.
9. Admin approves mockups.
10. Product moves to `admin_review`.
11. Admin approves product.
12. If required, school approves product.
13. Product becomes eligible for publishing.
14. Admin publishes product.
15. Product is assigned to team Shopify collection.
16. Product status becomes `published`.

Acceptance criteria:

- Product cannot be published unless:
  - Shopify product ID exists.
  - Shopify variant IDs exist.
  - Printful sync product ID exists.
  - Printful variant mappings are verified.
  - Mockups are approved.
  - Print files are approved.
  - Pricing is approved.
  - Revenue-share rule exists.
  - Permission records are valid.
- Failed syncs show clear error messages.
- Publish action writes audit log.
- Product detail page shows all external IDs.

---

## 7.4 Buyer Purchase and Order Import

Preconditions:

- Team storefront is published.
- Product is published.
- Product is visible in Shopify checkout experience.

Flow:

1. Buyer visits team storefront.
2. Buyer chooses product.
3. Buyer purchases through Shopify checkout.
4. Shopify records payment/order.
5. Shopify webhook notifies Convex.
6. Convex creates or updates `orders`.
7. Convex creates `orderItems`.
8. System attributes each order item to team/product/variant.
9. Printful fulfillment/cost data is synced.
10. Ledger entries are generated.

Acceptance criteria:

- Duplicate webhooks do not create duplicate orders.
- Orders are idempotently upserted by Shopify order ID.
- Order items are tied to team where possible.
- Unknown product mappings are flagged.
- Ledger entries are generated only once per event.
- Order appears in admin dashboard.

---

## 7.5 Monthly Payout Batch

Preconditions:

- Orders and ledger entries exist.
- Reserve window has passed.
- Organization has revenue-share rule.
- Stripe connected account may or may not be ready.

Flow:

1. Admin opens Finance Command Center.
2. Admin clicks “Generate Monthly Payout Batches.”
3. System aggregates eligible ledger entries by organization/team.
4. System creates draft payout batch.
5. System calculates:
   - gross sales
   - discounts
   - refunds
   - fulfillment costs
   - fees
   - net profit
   - school share
   - platform share
6. Admin reviews payout batch.
7. If data is valid, admin approves.
8. If Stripe account is ready, admin sends payout.
9. System triggers Stripe Connect transfer.
10. System records transfer ID.
11. System generates payout statement.
12. School dashboard updates.

Acceptance criteria:

- Payout batches cannot be sent unless approved by super admin.
- Payout batches cannot be sent if account status is restricted.
- Payout batches cannot be sent before reserve window passes.
- Payout batches cannot be sent if reconciliation anomaly exists unless manually overridden by super admin.
- Every payout is traceable to ledger entries.
- Monthly statement asset is generated or queued.
- Audit log records approval and transfer.

---

## 7.6 School Finance Admin Reviews Reports

Preconditions:

- School finance admin is authenticated.
- User is member of organization.
- Reports exist.

Flow:

1. School finance admin opens dashboard.
2. User sees active teams.
3. User sees fundraiser sales summary.
4. User sees payout history.
5. User downloads monthly statement PDF.
6. User downloads CSV export if needed.
7. User opens Stripe onboarding link if not complete.

Acceptance criteria:

- School finance admin sees only their organization.
- User cannot edit payout calculation.
- User cannot see unrelated organizations.
- User can download report assets.
- Report clearly shows Shopify sales, Printful fulfillment costs, ShopGo calculation, and Stripe payout status.

---

# 8. Functional Requirements

## 8.1 Authentication and Authorization

FR-001: The app must authenticate users through Clerk.  
FR-002: The app must create or sync a Convex `users` record for authenticated users.  
FR-003: The app must support global roles: `super_admin`, `operator`, `support`, `none`.  
FR-004: The app must support organization roles: `school_finance_admin`, `athletic_director`, `coach`, `booster_admin`, `viewer`.  
FR-005: Convex must enforce authorization checks server-side.  
FR-006: School users must only access records belonging to their organization.  
FR-007: Sensitive actions must write audit logs.

Sensitive actions include:

- approving product
- publishing product
- changing revenue-share rule
- approving payout
- sending payout
- freezing payouts
- pausing storefront
- unpublishing product
- changing permission status

---

## 8.2 Admin Dashboard

FR-008: Super admin must see global dashboard summary.  
FR-009: Dashboard must show active teams, pending approvals, failed integrations, estimated payouts, recent orders, and agent failures.  
FR-010: Admin must be able to search/filter organizations.  
FR-011: Admin must be able to search/filter teams.  
FR-012: Admin must be able to view integration health.  
FR-013: Admin must be able to view audit logs.  
FR-014: Dashboard UI should follow provided screenshots where available.

---

## 8.3 Organizations and Teams

FR-015: Admin can create organization.  
FR-016: Admin can edit organization.  
FR-017: Admin can archive organization.  
FR-018: Admin can create team under organization.  
FR-019: Admin can edit team.  
FR-020: Admin can pause/archive team.  
FR-021: Team slug must be globally unique.  
FR-022: Team slug should be generated from school/team identity.  
FR-023: Team storefront record should be created from team record.  
FR-024: Team detail page must show status, storefront URL, products, approvals, finance summary, permissions, and audit log.

---

## 8.4 Permissions

FR-025: Admin can create permission record.  
FR-026: Permission types must include school name, mascot, logo, colors, team name, fundraiser, and booster authorization.  
FR-027: Permission record must support status, document asset, granted by, valid from, valid until, and notes.  
FR-028: Product/storefront publishing must check required permissions.  
FR-029: Expired or denied permissions must block publishing unless super admin override is explicitly implemented and audited.

---

## 8.5 Brand Kits and Assets

FR-030: Admin can create brand kit.  
FR-031: Admin can upload school logos, mascot images, print files, mockups, hero images, permission docs, reports, and receipts.  
FR-032: Assets must have owner, type, storage provider, status, and metadata.  
FR-033: Print files must be approved before product can publish.  
FR-034: Mockups must be approved before product can publish.  
FR-035: Financial reports must be immutable after finalization.

---

## 8.6 Product Templates

FR-036: Admin can create product template.  
FR-037: Product template must map to Printful catalog product ID.  
FR-038: Product template must define allowed placements, colors, sizes, variants, default pricing, and category.  
FR-039: Product templates can be active/inactive.  
FR-040: Inactive templates cannot be used for new starter packs.

---

## 8.7 Product Pipeline

FR-041: Admin can create draft product.  
FR-042: Admin can create starter pack from templates.  
FR-043: Product must track status across pipeline.  
FR-044: Product must store Shopify product ID when synced.  
FR-045: Product must store Printful sync product ID when synced.  
FR-046: Product variants must store Shopify variant ID and Printful variant ID/sync variant ID.  
FR-047: Product variant mapping must support statuses: `unmapped`, `mapped`, `verified`, `error`.  
FR-048: Product cannot publish unless all required go-live conditions pass.  
FR-049: Product detail page must show missing requirements.

Required product publishing gates:

- valid team
- valid storefront
- valid permission records
- approved print files
- approved mockups
- pricing approved
- revenue-share rule attached
- Shopify product ID exists
- Shopify variant IDs exist
- Printful sync product ID exists
- Printful variant mappings verified
- product assigned to Shopify collection

---

## 8.8 Shopify Integration

FR-050: App must store Shopify product IDs.  
FR-051: App must store Shopify variant IDs.  
FR-052: App must store Shopify collection IDs/handles.  
FR-053: App must create or sync team collections.  
FR-054: App must assign products to team collections.  
FR-055: App must import Shopify orders from webhooks.  
FR-056: App must import Shopify refunds from webhooks.  
FR-057: Webhook processing must be idempotent.  
FR-058: Shopify sync errors must be visible in Integration Health.  
FR-059: Shopify product records should include stable ShopGo metafields/tags.

Required Shopify metafields/tags:

```txt
shopgo_team_id
shopgo_team_slug
shopgo_org_id
shopgo_product_id
printful_sync_product_id
sport
school
mascot
fundraiser
```

---

## 8.9 Printful Integration

FR-060: App must store Printful catalog product IDs on product templates.  
FR-061: App must create or track Printful sync products.  
FR-062: App must verify Shopify variant ↔ Printful variant mapping.  
FR-063: App must request or track Printful mockup generation.  
FR-064: App must sync Printful fulfillment status.  
FR-065: App must attach actual Printful costs to order items where available.  
FR-066: Missing Printful cost must create reconciliation warning.  
FR-067: Missing Printful variant mapping must block product publishing.

---

## 8.10 Stripe Connect

FR-068: App must create or track Stripe connected accounts for organizations.  
FR-069: App must create onboarding links.  
FR-070: App must sync connected account status.  
FR-071: App must track whether payouts are enabled.  
FR-072: App must create Stripe transfers only after payout batch approval.  
FR-073: App must store Stripe transfer ID.  
FR-074: App must block transfers when account is restricted, payout disabled, payout batch not approved, reserve window not passed, or reconciliation anomalies exist.  
FR-075: Stripe must not be represented as the buyer checkout provider in the MVP.

---

## 8.11 Orders and Ledger

FR-076: App must store Shopify order snapshots.  
FR-077: App must store order item records.  
FR-078: App must attribute order items to team/product/variant where possible.  
FR-079: App must generate immutable ledger entries.  
FR-080: Ledger corrections must be reversal/new entry, not updates.  
FR-081: Ledger entries must support gross sale, discount, refund, tax, shipping, fees, Printful cost, school commission, platform margin, payout transfer, payout reversal, reserve hold, reserve release, and manual adjustment.  
FR-082: Reports and payout batches must be generated from ledger entries.

---

## 8.12 Payouts and Reporting

FR-083: App must generate payout batches by organization and period.  
FR-084: App must apply reserve window.  
FR-085: App must calculate gross sales, refunds, costs, fees, net profit, school share, platform share.  
FR-086: App must support manual review before approval.  
FR-087: App must support super-admin approval.  
FR-088: App must generate monthly statement records.  
FR-089: App must generate yearly summary records.  
FR-090: School finance users must be able to download reports.  
FR-091: Reports must explain Shopify checkout, Printful fulfillment, ShopGo calculation, and Stripe payout status.

---

## 8.13 Manus Orchestration

FR-092: App must store Manus task records.  
FR-093: Admin/operator can create Manus task prompt from team/storefront context.  
FR-094: Manus tasks must have statuses.  
FR-095: Manus changes to Shopify should be followed by Shopify sync/diff check.  
FR-096: Manus must not approve payouts, change revenue-share rules, or determine permission validity.

---

## 8.14 Agent Runs

FR-097: App must store agent runs.  
FR-098: Agent runs must have type, status, input, output, logs, error, timestamps, and optional linked team/product/org.  
FR-099: Agents must not perform high-risk final actions without human approval.  
FR-100: Ops Monitor Agent must flag stuck jobs, missing mappings, missing costs, restricted Stripe accounts, expired permissions, missing Shopify mappings, missing Printful mappings, and unattributed orders.

---

# 9. Non-Functional Requirements

## 9.1 Security

NFR-001: All server-side functions must enforce authorization.  
NFR-002: API keys and secrets must not be stored in plaintext application tables.  
NFR-003: Buyer PII should remain in Shopify unless operationally necessary.  
NFR-004: If buyer email is needed, store hash where possible.  
NFR-005: Bank/tax information must be handled by Stripe, not stored in ShopGo.  
NFR-006: Webhooks must verify signatures where available.  
NFR-007: Webhooks must be idempotent.

## 9.2 Reliability

NFR-008: Failed external sync operations must be retryable.  
NFR-009: Product publishing must be gate-checked.  
NFR-010: Payout sending must be gate-checked.  
NFR-011: Ledger entries must be immutable.  
NFR-012: External IDs must be persisted for reconciliation.

## 9.3 Performance

NFR-013: Admin dashboard summary should load within 2 seconds for MVP-scale data.  
NFR-014: Team detail page should load within 2 seconds for MVP-scale data.  
NFR-015: Large exports may be generated asynchronously.

## 9.4 Scalability

NFR-016: Data model must support many schools, many teams, many products, and many orders.  
NFR-017: Team storefront must be core object.  
NFR-018: Product pipeline must support additional fulfillment providers later.  
NFR-019: Stripe account model should not hard-code only one account type forever.  
NFR-020: Storefront renderer should support Manus, Next.js, Shopify theme, or hybrid.

## 9.5 Maintainability

NFR-021: Integration code should be isolated by provider.  
NFR-022: Finance logic should live in dedicated finance/ledger module.  
NFR-023: Approval gates should be reusable utilities.  
NFR-024: Schema names should match Technical Specification v0.3.  
NFR-025: Use TypeScript types consistently.

---

# 10. UX Requirements

The implementation should follow UI/UX screenshots placed in the same folder as this PRD. Codex should inspect those screenshots and infer layout, hierarchy, visual style, spacing, and component patterns.

If screenshots conflict with this PRD, treat this PRD as source of truth for behavior and the screenshots as source of truth for visual direction.

## 10.1 Required App Areas

Admin app routes:

```txt
/admin
/admin/organizations
/admin/organizations/[organizationId]
/admin/teams
/admin/teams/[teamId]
/admin/storefronts
/admin/products
/admin/products/[productId]
/admin/approvals
/admin/finance
/admin/payouts/[payoutBatchId]
/admin/integrations
/admin/audit-log
/admin/agent-runs
```

School dashboard routes:

```txt
/school
/school/teams
/school/teams/[teamId]
/school/reports
/school/payouts
/school/approvals
/school/settings
```

Public storefront route:

```txt
/[teamSlug]
```

Optional MVP placeholder if Manus/Shopify storefront handles rendering:

```txt
/[teamSlug] redirects to or embeds the active storefront destination.
```

## 10.2 Status-Driven UI

Every operational object should show clear status badges:

- organization status
- team status
- storefront status
- product status
- mapping status
- approval status
- payout status
- integration status
- agent run status

## 10.3 Empty States

Empty states should guide users toward the next action.

Examples:

- “No teams yet. Create your first team storefront.”
- “No products yet. Create a starter pack.”
- “No payout batches yet. Generate a payout batch after sales are imported.”
- “No reports yet. Reports are generated after payout batches are approved.”

## 10.4 Error States

Errors should be plain-language and actionable.

Examples:

- “This product cannot be published because three variants are missing verified Printful mappings.”
- “This payout cannot be sent because the connected Stripe account is restricted.”
- “This storefront cannot go live because fundraiser permission is missing.”

---

# 11. Data Model Requirements

Codex must implement the Convex schema from Technical Specification v0.3 as closely as possible.

Required tables:

```txt
users
organizations
organizationMembers
contacts
teams
teamStorefronts
brandKits
permissions
assets
productTemplates
products
productVariants
orders
orderItems
revenueShareRules
ledgerEntries
payoutBatches
stripeConnectedAccounts
reports
integrationAccounts
agentRuns
approvalRequests
auditLogs
manusTasks
webhookEvents
```

Implementation note:

If early MVP needs to stub some external integration fields, keep schema-compatible placeholders rather than changing the conceptual model.

---

# 12. Sprint Plan

## Sprint 0 — Project Setup and Foundations

Goal:

Set up the application foundation and developer workflow.

Deliverables:

- Next.js app initialized.
- Convex configured.
- Clerk configured.
- Environment variable structure.
- Basic layout shell.
- Role-aware navigation placeholder.
- Initial schema scaffold.
- Seed script for super admin and sample organization/team.
- README with local development instructions.

Acceptance criteria:

- Developer can run app locally.
- User can sign in.
- Convex functions compile.
- Admin shell loads.
- Sample data appears.
- No secrets committed.

---

## Sprint 1 — Auth, Roles, Admin Shell

Goal:

Implement Clerk auth and Convex-side authorization.

Deliverables:

- Clerk sign-in/sign-up.
- Convex user sync.
- Global role support.
- Organization member support.
- Admin layout.
- School layout.
- Access guards.
- Audit log utility.
- Basic dashboard placeholder.

Acceptance criteria:

- Super admin can access `/admin`.
- Non-admin cannot access `/admin`.
- School user can access `/school`.
- School user cannot access other org data.
- Sensitive access checks happen server-side.
- Audit log utility can be called by mutations.

---

## Sprint 2 — Organizations, Teams, Storefronts

Goal:

Build core school/team/storefront management.

Deliverables:

- Organizations CRUD.
- Teams CRUD.
- Team slug generation.
- Team storefront creation.
- Team detail page.
- Organization detail page.
- Status badges.
- Audit logs for create/update/archive.
- Public URL preview.

Acceptance criteria:

- Admin can create organization.
- Admin can create team under organization.
- Slugs are globally unique.
- Team storefront record is created.
- Team detail shows storefront URL.
- Team can be paused/archived.
- Audit logs are written.

---

## Sprint 3 — Brand Kits, Permissions, Assets

Goal:

Add the trust and brand-control layer.

Deliverables:

- Asset upload metadata flow.
- Brand kit create/edit.
- Permission records.
- Permission document attachment.
- Permission status UI.
- Product/storefront publish gate helper begins.
- File status badges.

Acceptance criteria:

- Admin can upload/link assets.
- Admin can create brand kit.
- Admin can create permission records.
- Permission records can attach documents.
- Expired/denied/missing permissions are visible.
- Gate helper can report missing permissions.

---

## Sprint 4 — Product Templates and Product Pipeline

Goal:

Build product creation flow before external integrations are fully live.

Deliverables:

- Product templates CRUD.
- Product starter pack creation.
- Products table/list.
- Product detail page.
- Product status pipeline.
- Product variants.
- Product publishing gate checklist.
- Mock external IDs allowed for testing.

Acceptance criteria:

- Admin can define product templates.
- Admin can create starter pack.
- Products appear in pipeline.
- Product detail shows required publish gates.
- Product cannot publish if required fields missing.
- Variants can be added/edited.
- Mapping status is visible.

---

## Sprint 5 — Printful Integration Layer

Goal:

Add Printful-specific product sync/mapping readiness.

Deliverables:

- Printful provider module.
- Catalog product lookup placeholder or live integration.
- Printful sync product ID tracking.
- Printful sync variant mapping tracking.
- Mockup task tracking.
- Printful fulfillment status fields.
- Mapping verification function.

Acceptance criteria:

- Product can store Printful sync product ID.
- Variants can store Printful IDs.
- Mapping can be marked verified.
- Missing mapping blocks publishing.
- Mockup assets can be attached.
- Printful-related errors show in Integration Health.

---

## Sprint 6 — Shopify Integration Layer

Goal:

Sync products and collections to Shopify.

Deliverables:

- Shopify provider module.
- Team collection creation.
- Shopify product ID tracking.
- Shopify variant ID tracking.
- Product-to-collection assignment.
- Shopify webhook receiver scaffold.
- Shopify sync/diff logging.

Acceptance criteria:

- Team storefront can store Shopify collection ID.
- Product can store Shopify product ID.
- Variants can store Shopify variant IDs.
- Product can be assigned to collection.
- Product cannot publish without Shopify/Printful mappings.
- Shopify webhook event can be stored idempotently.

---

## Sprint 7 — Publishing Workflow and Approvals

Goal:

Complete human-in-the-loop approval and publishing controls.

Deliverables:

- Approval requests.
- Admin review queue.
- School review queue.
- Product approval flow.
- Storefront approval flow.
- Publish/unpublish actions.
- Gate enforcement.
- Audit logging.

Acceptance criteria:

- Admin can approve/reject product.
- School user can approve/reject assigned approval.
- Product publish action checks all gates.
- Storefront cannot go live with invalid product mappings.
- Rejections store decision notes.
- Publish/unpublish writes audit log.

---

## Sprint 8 — Orders, Webhooks, Ledger

Goal:

Import Shopify orders and generate finance ledger entries.

Deliverables:

- Orders table UI.
- Order detail page.
- Shopify order webhook handling.
- Order item creation.
- Team/product attribution.
- Ledger entry generation.
- Refund handling scaffold.
- Reconciliation warning for missing mapping/cost.

Acceptance criteria:

- Shopify order webhook creates order once.
- Duplicate webhook does not duplicate records.
- Order items are created.
- Ledger entries are generated.
- Missing attribution creates warning.
- Refund event creates ledger adjustment.
- Admin can view ledger entries.

---

## Sprint 9 — Printful Costs and Fulfillment Reconciliation

Goal:

Attach fulfillment/cost data to orders.

Deliverables:

- Printful fulfillment webhook handling.
- Fulfillment status sync.
- Actual cost attachment.
- Missing cost warnings.
- Reconciliation status view.
- Ops monitor checks.

Acceptance criteria:

- Order items can store actual Printful cost.
- Fulfillment status appears on order item.
- Missing cost is flagged.
- Reconciliation Agent/Ops Monitor can list anomalies.
- Admin can mark anomaly reviewed.

---

## Sprint 10 — Payout Batches and Stripe Connect

Goal:

Create approved post-sale school payout workflow.

Deliverables:

- Stripe connected accounts table/UI.
- Onboarding link generation.
- Account status sync.
- Payout batch generation.
- Reserve window logic.
- Payout approval workflow.
- Stripe transfer action.
- Payout receipt/status tracking.

Acceptance criteria:

- Organization can have Stripe connected account.
- Onboarding status is visible.
- Payout batch can be generated from ledger.
- Payout batch requires approval.
- Payout cannot be sent if gates fail.
- Stripe transfer ID is stored after send.
- Audit logs record approval and transfer.

---

## Sprint 11 — School Dashboard and Reports

Goal:

Give schools finance-friendly visibility.

Deliverables:

- School dashboard.
- Team sales summary.
- Payout history.
- Monthly statement view.
- Report download records.
- Yearly summary scaffold.
- Stripe onboarding access.

Acceptance criteria:

- School user sees only their org.
- School user sees team storefronts.
- School user sees payout history.
- School user can download reports.
- Reports clearly separate Shopify sales, Printful fulfillment costs, ShopGo calculation, and Stripe payout status.
- School user cannot edit ledger or payout math.

---

## Sprint 12 — Manus Tasks and Agent Runs

Goal:

Add orchestration layer for assisted operations.

Deliverables:

- Manus task table/UI.
- Prompt templates.
- Manus task creation from team/storefront/product context.
- Agent run table/UI.
- Ops Monitor Agent scaffold.
- Reconciliation Agent scaffold.
- Report Agent scaffold.
- Manual status updates for agent runs.

Acceptance criteria:

- Admin can create Manus task.
- Manus task stores prompt and status.
- Admin can view agent runs.
- Agent run can be linked to team/product/org.
- Ops monitor flags missing mappings/costs/stuck statuses.
- Agents do not perform high-risk actions without approval.

---

# 13. Definition of Done

A story is done when:

- Server-side authorization is implemented.
- UI reflects loading, empty, success, and error states.
- Data writes are audited when sensitive.
- Required Convex indexes exist.
- TypeScript compiles.
- Lint passes.
- No secrets are committed.
- Relevant acceptance criteria pass.
- Manual QA path is documented.
- If external integration is mocked, mock is clearly labeled and replaceable.

---

# 14. MVP Release Criteria

The MVP is releasable when:

1. Super admin can create school/team/storefront.
2. Product starter pack can be created.
3. Product variants can be mapped to Shopify/Printful IDs.
4. Products cannot publish without verified mapping and approvals.
5. Shopify orders can be imported or simulated.
6. Ledger entries are generated from orders.
7. Payout batch can be generated from ledger.
8. Payout batch can be approved by super admin.
9. Stripe connected account status can be tracked.
10. School dashboard can show sales and payout/report summaries.
11. Audit logs exist for sensitive actions.
12. Integration health page shows Shopify, Printful, Stripe, Manus, and agent status.
13. Core UI matches provided screenshots closely enough for initial usability.

---

# 15. Known Risks and Mitigations

## Risk: Product goes live without Printful sync mapping

Mitigation:

Publishing gate must require verified Shopify variant ↔ Printful variant mapping.

## Risk: School disputes payout amount

Mitigation:

Use immutable ledger entries and downloadable monthly statements.

## Risk: Stripe is misunderstood as buyer payment processor

Mitigation:

Use canonical language throughout UI and docs:

```txt
Buyers purchase through Shopify checkout.
Stripe Connect powers post-sale school payouts.
```

## Risk: Manus changes Shopify data outside Convex expectations

Mitigation:

Run Shopify sync/diff after Manus tasks and write audit log.

## Risk: School lacks authorization to use logos/mascots

Mitigation:

Permission records must block publishing if missing, denied, or expired.

## Risk: Shopify/Printful/Stripe webhooks duplicate events

Mitigation:

Store webhook event IDs and process idempotently.

## Risk: Payout sent too early before refunds/costs settle

Mitigation:

Use reserve window and manual super-admin approval.

## Risk: Buyer PII exposure

Mitigation:

Keep buyer PII in Shopify where possible; store hashed email only if needed.

---

# 16. Open Questions

These must be resolved before full production release, but do not need to block early internal MVP.

1. Will public storefronts be Manus-served, Next.js-served, Shopify-theme-served, or hybrid?
2. Will each team have one collection or multiple sport/category collections?
3. Will school share be percentage of net merch profit, fixed amount per item, or configurable by product?
4. Will payout recipients usually be schools, booster clubs, athletic departments, or districts?
5. Will the first market be U.S. schools, Canadian schools, or both?
6. Will ShopGo require written permission before product creation or only before publishing?
7. What is the default reserve window: 7 days, 14 days, or 30 days?
8. What is the minimum payout threshold?
9. Should school users be able to invite other users?
10. Should operators be able to change product pricing, or only super admins?

---

# 17. Codex Implementation Guidance

Codex should read this PRD together with:

- Technical Specification v0.3
- UI/UX screenshots in the same folder
- Any implementation notes or environment files

Build order should follow the sprint plan. Do not jump directly to integrations before the core schema, permissions, product pipeline, and ledger model exist.

Prioritize:

1. Correct data model.
2. Correct authorization.
3. Correct workflow states.
4. Correct publishing gates.
5. Correct ledger model.
6. Clear admin UI.
7. Replaceable integration modules.
8. Human approval before high-risk actions.

Avoid:

- building direct checkout inside ShopGo
- treating Stripe as buyer checkout
- making Manus the system of record
- allowing products to publish without Printful mapping
- calculating payouts from raw orders without ledger entries
- allowing school users to edit financial logic
- storing raw bank details
- over-automating approvals before manual workflow is stable

---

# 18. Suggested Initial File Structure

```txt
/app
  /(admin)
    admin/
      page.tsx
      organizations/
      teams/
      products/
      approvals/
      finance/
      integrations/
      audit-log/
      agent-runs/
  /(school)
    school/
      page.tsx
      teams/
      reports/
      payouts/
      approvals/
  /[teamSlug]/
    page.tsx

/components
  /admin
  /school
  /shared
  /status
  /forms
  /tables

/convex
  schema.ts
  auth.ts
  users.ts
  organizations.ts
  teams.ts
  storefronts.ts
  products.ts
  assets.ts
  permissions.ts
  approvals.ts
  orders.ts
  ledger.ts
  payouts.ts
  reports.ts
  auditLogs.ts
  agentRuns.ts
  manusTasks.ts
  webhooks.ts
  http.ts

/lib
  /auth
  /permissions
  /status
  /money
  /formatting
  /integrations
    shopify.ts
    printful.ts
    stripe.ts
    manus.ts
  /finance
    ledger.ts
    payoutCalculations.ts
    reconciliation.ts
  /agents
    schoolIntake.ts
    brandKit.ts
    productBuilder.ts
    opsMonitor.ts
    financeReport.ts

/docs
  PRD.md
  Technical-Spec-v0.3.md
  screenshots/
```

---

# 19. Build Philosophy

Build ShopGo as an admin-first operating system.

The product must feel empowering for ShopGo operators and reassuring for schools. The system should reduce chaos, not hide it. Every important object should have a status. Every risky action should have a gate. Every payout should have a paper trail. Every published product should have verified fulfillment mapping.

The competitive advantage is not simply that ShopGo can create merch quickly. The durable advantage is that ShopGo can launch team stores quickly while giving schools financial confidence, operational transparency, and clean fundraiser reporting.
