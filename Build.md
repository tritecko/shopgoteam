# ShopGo.team Build Runbook

Version: 1.0
Companion documents: `PRD.md`, `ShopGo.team Technical Specification v0.3.md`, `design.md`
Target stack: Next.js + Convex + Clerk + Shopify + Printful + Stripe Connect
Primary execution style: checkbox-driven, test-first, human-approved

---

## Execution Status

Overall status: Stage 0 complete; Stage 1 complete; Stage 2 ready
Current stage: Stage 1 - Pencil Design System (complete)
Next unchecked task: Scaffold Next.js App Router with pnpm and strict TypeScript
Last verification: Pencil Desktop MCP confirmed the saved canonical
  `design/shopgo-team.pen`; all accepted desktop/mobile frames pass layout and
  visual review; 18 semantic 2x PNG exports, the export manifest, and the
  serialized Pencil variables were verified on 2026-06-13
Active blockers: None
Last updated: 2026-06-13

## How To Use This Runbook

This file is the execution source of truth for building ShopGo.team. The PRD
defines product scope, the technical specification defines domain behavior and
data requirements, and `design.md` defines the intended visual direction.

Before changing code, every agent session must:

1. Read `PRD.md`, the technical specification, `design.md`, and this file.
2. Read the latest entry in **Session Handoffs**.
3. Confirm the current stage and exact next unchecked task.
4. Inspect the working tree and preserve changes from earlier sessions.
5. Update the status block before ending the session.

Checkbox rules:

- Check a task only after its acceptance evidence passes.
- Check a stage gate only after every required task in that stage passes.
- Run only one stage as active at a time.
- Never remove completed checkboxes or prior handoff entries.
- Record external Shopify, Printful, Clerk, Convex, Stripe, and Vercel changes.
- Record verification commands and outcomes, not just that verification occurred.
- Do not perform production deployment, customer-visible publishing, live money
  movement, or destructive provider mutations without explicit product-owner
  approval at action time.

## Canonical Operating Model

```txt
Supporters browse and buy merchandise at goteam.shop.
Shopify owns the public storefront, cart, checkout, payment, and order record.
Printful owns POD product mapping, production, fulfillment, packing, and shipping.
Convex owns ShopGo business state, workflows, ledger, reports, and audit history.
Clerk owns identity, sessions, invitations, and organization membership lifecycle.
Stripe Connect sends approved post-sale school payouts from funded ShopGo balances.
```

`shopgo.team` hosts the authenticated ShopGo admin and school portals.
`goteam.shop` remains the buyer-facing Shopify storefront and checkout.

## Locked Architecture

- Next.js App Router, strict TypeScript, Tailwind CSS, and accessible
  Radix/shadcn-style primitives, deployed through Vercel.
- Pencil is the production UI design source. Approved `.pen` screens and exported
  design tokens precede implementation of production UI.
- Convex owns business records, authorization, workflows, integration state,
  audit history, immutable ledger entries, payout batches, and reports.
- Clerk owns authentication, sessions, invitations, user management, and Clerk
  Organizations.
- Convex mirrors Clerk users and organization memberships and remains
  authoritative for business permissions.
- One central Shopify store serves the MVP.
- Each team receives one manually controlled Shopify collection.
- Stable ShopGo IDs are stored in Shopify metafields.
- Printful owns POD products, variant mappings, mockups, costs, and fulfillment.
- Stripe Connect Accounts v2 provides U.S. school or booster onboarding and
  post-sale payouts.
- Shopify proceeds settle outside Stripe. ShopGo must fund its Stripe platform
  balance before transferring approved amounts to connected accounts.
- Live Stripe transfers remain disabled until funding, responsibilities, legal
  eligibility, and operational controls are verified.
- Manus and agents may prepare work but never hold final authority for permission,
  publishing, ledger, payout, or financial-report decisions.

## Locked MVP Defaults

| Area | MVP default |
|---|---|
| Launch market | United States |
| Currency | USD |
| Buyer storefront | Shopify at `goteam.shop` |
| Authenticated portals | Next.js at `shopgo.team` |
| Shopify model | One central store |
| Team merchandising | One manual collection per team |
| Product ownership | One team per product |
| School share | Configurable percentage of net merch profit |
| Taxes | Excluded from fundraiser earnings |
| Shipping | Excluded from fundraiser earnings |
| Payout cadence | Monthly |
| Reserve window | 14 days |
| Minimum payout | $25 |
| External APIs in CI | Mocked |
| Production mutations | Explicit approval required |

## Public Interfaces And Types

Standardize these domain types before feature implementation:

```ts
type Currency = "USD";

type Money = {
  amountCents: number;
  currency: Currency;
};

type ProviderError = {
  provider: "shopify" | "printful" | "stripe" | "clerk";
  code: string;
  message: string;
  retryable: boolean;
  requestId?: string;
};

type PublishBlocker = {
  code: string;
  label: string;
  severity: "error" | "warning";
  entityType?: string;
  entityId?: string;
};

type PublishEligibility = {
  eligible: boolean;
  blockers: PublishBlocker[];
};

type PayoutEligibility = {
  eligible: boolean;
  blockers: Array<{
    code: string;
    label: string;
  }>;
};
```

Also define:

- `NormalizedOrder` for provider-independent Shopify order ingestion.
- `LedgerPosting` for balanced, append-only financial postings.
- Shopify, Printful, and Stripe provider interfaces with mock and production
  implementations.
- Typed provider results that do not leak raw provider payloads into domain logic.

Required backend operations:

```txt
organizations.create
teams.createWithStorefront
products.createStarterPack
products.getPublishEligibility
products.requestApproval
products.publishToShopify
orders.ingestShopifyEvent
reconciliation.run
payouts.generateBatch
payouts.approveBatch
payouts.sendTransfer
reports.generateStatement
```

## Convex Data Rules

- Every tenant-owned table includes `organizationId`.
- Tenant indexes begin with `organizationId` and then match the screen's filter
  and sort pattern.
- User-facing lists are paginated; do not call `collect()` on unbounded tables.
- Queries and mutations perform server-side membership and role checks.
- Mutations own transactional database writes.
- Actions are reserved for provider calls and non-transactional work.
- Multi-step synchronization, reconciliation, reporting, and payout processes use
  durable workflows with persisted progress and retry-safe steps.
- Binary files live in Convex Storage; tables store storage IDs and metadata.
- Ledger entries are append-only. Corrections use reversal and replacement entries.
- `payoutBatchEntries` links immutable ledger entries to payout batches; payout
  generation never edits ledger entries.
- `reconciliationAnomalies` records missing mappings, costs, duplicates, total
  mismatches, resolution state, and reviewer evidence.
- Webhook records store provider, external event identity, payload hash,
  processing status, attempts, timestamps, and restricted payload storage
  references.
- Raw payloads containing personal or sensitive information are not returned to
  ordinary client queries.

## Provider Boundaries

### Shopify

- The Shopify CLI connector is for operator setup and store inspection.
- Production integrations use dedicated Shopify app credentials and webhook
  subscriptions, not a developer's CLI session.
- Perform a read-only inventory before proposing mutations.
- Pin the Admin GraphQL API version.
- Validate GraphQL operations with the Shopify plugin before implementation.
- Draft products and duplicate themes are used before customer-visible publishing.

### Printful

- Product templates map to Printful catalog product and variant IDs.
- Every active Shopify variant requires a verified Printful sync mapping.
- Actual costs replace estimated costs before payout eligibility.
- Mockup and fulfillment operations are asynchronous and retry-safe.

### Clerk

- Clerk authenticates identity and manages organization lifecycle.
- Convex authorizes every business operation.
- Clerk webhook signatures are verified before user or membership synchronization.
- A Clerk role or client-side route check never replaces Convex authorization.

### Stripe

- Use Accounts v2 and explicit controller, dashboard, responsibility, and
  capability configuration.
- Connected account eligibility, reserve completion, anomaly status, approval,
  and available platform balance are checked before transfer.
- Shopify revenue does not automatically fund Stripe.
- Platform-balance funding is an explicit operational prerequisite.
- Live transfers are protected by a production feature flag and explicit approval.

## Required Test Layers

| Layer | Coverage |
|---|---|
| Unit | Money math, slug generation, role checks, states, publish and payout gates |
| Convex | Tenant isolation, indexes, transactional writes, ledger immutability, pagination |
| Component | Forms, status controls, inline action stages, tables, empty/error/loading states |
| Integration | Mock Shopify, Printful, Clerk, Stripe, webhook parsing and normalization |
| E2E | Admin onboarding, product publication, order import, payout approval, school reporting |
| Security | Auth bypass, tenant escape, upload abuse, webhook replay, payout bypass |
| Accessibility | Keyboard navigation, focus, labels, status semantics, reduced motion |
| Visual | Pencil-to-browser comparison at approved desktop and mobile sizes |

Default local verification after the application exists:

```bash
pnpm lint
pnpm typecheck
pnpm test
pnpm build
```

Run targeted Playwright tests for UI stories and the complete E2E suite at release
gates.

---

# Build Stages

## Stage 0 - Prerequisites And Governance

Goal: establish a safe, reproducible workspace and execution contract.

- [x] Initialize Git and establish a clean baseline.
- [x] Remove the duplicated half of the technical specification.
- [x] Install and verify Pencil CLI with `npm install -g @pencil.dev/cli`.
- [x] Authenticate Pencil CLI.
- [x] Open Pencil desktop or IDE extension and confirm `.pen` editing works.
- [x] Install and verify Clerk CLI with `npm install -g clerk`.
- [x] Verify or install Shopify CLI 3.93 or newer.
- [x] Obtain the store's `.myshopify.com` or Shopify admin URL.
- [x] Connect Shopify CLI to the existing store with only required scopes.
- [x] Record provider environments and prohibit production mutations by default.
- [x] Create `AGENTS.md`, `.env.example`, `README.md`, `.gitignore`, and
  `docs/DECISIONS.md`.
- [x] Append a verified Stage 0 session handoff.
- [x] **Stage 0 Gate:** all required tools work and no secrets exist in tracked files.

Evidence required:

- Git status and baseline commit.
- CLI version outputs.
- Authentication or connection status without exposing secrets.
- Secret-pattern scan of tracked files.
- Links to the created governance files.

## Stage 1 - Pencil Design System

Goal: create and approve the design source before implementing production UI.

- [x] Create the ShopGo Pencil design document.
- [x] Define colors, typography, spacing, shadows, radii, icons, and responsive rules.
- [x] Design admin shell, school shell, tables, status controls, and inline action stages.
- [x] Design Admin Dashboard.
- [x] Design Team Detail.
- [x] Design Product Publish Gate.
- [x] Design Finance Command Center.
- [x] Design Payout Batch Detail.
- [x] Design School Dashboard.
- [x] Design Integration Health.
- [x] Audit `shopgo.team` read-only.
- [x] Design any proposed Shopify theme changes in Pencil.
- [x] Export approved assets and implementation tokens.
- [x] Record desktop and mobile acceptance screenshots.
- [x] **Stage 1 Gate:** product owner approves all primary desktop and mobile screens.

Evidence required:

- `.pen` file path.
- Exported screen and token paths.
- Product-owner approval record.

## Stage 2 - Application Foundation

Goal: create the tested Next.js, Convex, Clerk, and CI foundation.

- [ ] Scaffold Next.js App Router with pnpm and strict TypeScript.
- [ ] Add Convex and initialize the `convex/` backend.
- [ ] Run `npx convex ai-files install`.
- [ ] Read generated Convex AI guidelines before writing backend functions.
- [ ] Add Clerk and the Convex/Clerk provider wiring.
- [ ] Add Tailwind and accessible component primitives.
- [ ] Add Vitest, Testing Library, MSW, `convex-test`, and Playwright.
- [ ] Configure ESLint, typecheck, test, build, and CI scripts.
- [ ] Configure GitHub Actions.
- [ ] Configure Vercel preview builds.
- [ ] Implement approved Pencil tokens without feature logic.
- [ ] Add placeholder admin and school route shells.
- [ ] **Stage 2 Gate:** lint, typecheck, unit tests, and production build pass.

## Stage 3 - Clerk And Multitenant Authorization

Goal: authenticate users through Clerk and enforce tenant access in Convex.

- [ ] Configure Clerk middleware and protected portal routes.
- [ ] Configure `ConvexProviderWithClerk`.
- [ ] Configure `convex/auth.config.ts`.
- [ ] Use Clerk Organizations for school membership and invitations.
- [ ] Implement Convex users, organizations, and organization memberships.
- [ ] Verify and process Clerk user and membership webhooks.
- [ ] Implement `requireUser`.
- [ ] Implement `requireSuperAdmin`.
- [ ] Implement `requireOrganizationAccess`.
- [ ] Implement finance, publishing, and payout authorization helpers.
- [ ] Add audit logging for membership and role changes.
- [ ] Test inactive, removed, and cross-organization membership states.
- [ ] **Stage 3 Gate:** authorization tests prove tenant isolation.

## Stage 4 - Organizations, Teams, And Storefront References

Goal: manage schools and teams and connect them to Shopify collections.

- [ ] Implement organization records and contacts.
- [ ] Implement team records.
- [ ] Implement storefront reference records.
- [ ] Implement globally unique team slugs.
- [ ] Store Shopify collection ID, handle, and public URL per team.
- [ ] Use `https://goteam.shop/collections/{team-handle}` as the buyer destination.
- [ ] Build admin organization list and detail screens.
- [ ] Build admin team list and detail screens.
- [ ] Display storefront URL and integration status.
- [ ] Audit create, update, pause, archive, and slug changes.
- [ ] **Stage 4 Gate:** an admin can create a school, team, and storefront reference
  with an audit trail.

## Stage 5 - Brand Kits, Permissions, And Assets

Goal: enforce school-safe brand and authorization records.

- [ ] Implement Convex Storage-backed asset metadata.
- [ ] Validate file type, size, checksum, ownership, and access.
- [ ] Implement brand kits.
- [ ] Implement permission records.
- [ ] Implement permission expiry evaluation.
- [ ] Block publishing for missing, denied, or expired required permissions.
- [ ] Protect finalized permission evidence from ordinary edits.
- [ ] Build brand, asset, and permission interfaces from Pencil designs.
- [ ] Test tenant isolation and malicious upload cases.
- [ ] **Stage 5 Gate:** permission and file-security tests pass.

## Stage 6 - Product And Printful Pipeline

Goal: create the product workflow and verified fulfillment mappings.

- [ ] Implement product templates.
- [ ] Implement products and variants.
- [ ] Implement revenue-share rules.
- [ ] Create the starter-pack workflow.
- [ ] Implement product status transitions.
- [ ] Define the Printful provider interface and mock.
- [ ] Implement Printful catalog normalization.
- [ ] Implement sync product and variant mapping.
- [ ] Implement asynchronous mockup tasks.
- [ ] Implement cost and fulfillment normalization.
- [ ] Require verified Shopify-to-Printful mapping for every active variant.
- [ ] Build product pipeline and mapping interfaces.
- [ ] **Stage 6 Gate:** fixtures prove partial or invalid mappings cannot publish.

## Stage 7 - Existing Shopify Store Integration

Goal: safely connect Convex-managed team products to `goteam.shop`.

- [ ] Inventory products, collections, theme, apps, webhooks, and metafields read-only.
- [ ] Record the Shopify store handle and environment.
- [ ] Create or configure production Shopify app credentials separately from CLI.
- [ ] Store Shopify secrets only in approved environment stores.
- [ ] Pin and validate the Admin GraphQL API version.
- [ ] Define the Shopify provider interface and mock.
- [ ] Implement one manual team collection per team.
- [ ] Store stable ShopGo IDs in Shopify metafields.
- [ ] Create products in draft state.
- [ ] Assign products to team collections.
- [ ] Persist Shopify product and variant IDs in Convex.
- [ ] Use a duplicate theme for approved Pencil-driven theme changes.
- [ ] Reconcile Shopify state back into Convex after mutations.
- [ ] **Stage 7 Gate:** draft product and collection sync works without
  customer-visible changes.

## Stage 8 - Approvals And Publishing

Goal: enforce human approval and explain every publishing blocker.

- [ ] Implement approval requests and decisions.
- [ ] Implement role-specific approval queues.
- [ ] Implement centralized `products.getPublishEligibility`.
- [ ] Require valid permissions.
- [ ] Require approved pricing and revenue share.
- [ ] Require approved print files and mockups.
- [ ] Require Shopify product and collection assignment.
- [ ] Require complete Shopify-to-Printful variant mappings.
- [ ] Implement server-enforced publish, pause, and unpublish actions.
- [ ] Add confirmations for customer-visible actions.
- [ ] Add audit records for every high-risk action.
- [ ] Build the Pencil-approved gate checklist and inline action stage.
- [ ] **Stage 8 Gate:** Playwright verifies the complete blocked-to-published flow.

## Stage 9 - Shopify Orders, Webhooks, And Ledger

Goal: ingest commerce events once and create immutable financial records.

- [ ] Implement signed Shopify webhook endpoints.
- [ ] Store provider event identity and payload hash.
- [ ] Deduplicate events by provider and external event identity.
- [ ] Normalize orders and line items into `NormalizedOrder`.
- [ ] Attribute items using Shopify product and variant mappings.
- [ ] Create unresolved anomalies for unknown products or variants.
- [ ] Generate append-only ledger postings transactionally.
- [ ] Exclude taxes and shipping from fundraiser earnings by default.
- [ ] Generate reversal entries for refunds and corrections.
- [ ] Protect customer personal information.
- [ ] Build order and ledger views.
- [ ] **Stage 9 Gate:** duplicate webhooks cannot duplicate orders or ledger entries.

## Stage 10 - Printful Reconciliation

Goal: attach actual fulfillment costs and resolve data discrepancies.

- [ ] Import fulfillment status.
- [ ] Import actual Printful costs.
- [ ] Implement durable reconciliation workflows with retries.
- [ ] Detect missing costs.
- [ ] Detect unknown variants.
- [ ] Detect duplicate orders or fulfillment records.
- [ ] Detect order and cost total mismatches.
- [ ] Implement `reconciliationAnomalies`.
- [ ] Require human resolution or acknowledgement.
- [ ] Block payout eligibility while blocking anomalies remain unresolved.
- [ ] Build reconciliation and integration-health interfaces.
- [ ] **Stage 10 Gate:** fixtures cover every payout-blocking anomaly.

## Stage 11 - Stripe Connect And Payouts

Goal: calculate, approve, fund, and send auditable U.S. school payouts.

- [ ] Confirm Stripe platform profile and U.S. top-up availability.
- [ ] Configure Accounts v2 controller and responsibility settings.
- [ ] Implement connected-account onboarding.
- [ ] Synchronize account capabilities and requirements.
- [ ] Implement monthly payout batches.
- [ ] Enforce the 14-day reserve window.
- [ ] Enforce the $25 minimum payout.
- [ ] Implement `payoutBatchEntries`.
- [ ] Aggregate immutable ledger entries into payout batches.
- [ ] Implement centralized payout eligibility.
- [ ] Require super-admin approval.
- [ ] Verify Stripe available platform balance before transfer.
- [ ] Document and support manual platform-balance funding.
- [ ] Implement transfer idempotency and failure recovery.
- [ ] Protect live transfers with a production feature flag.
- [ ] Audit approvals, funding evidence, transfers, failures, and reversals.
- [ ] **Stage 11 Gate:** test-mode transfers pass and every blocker fails safely.

## Stage 12 - School Portal And Reports

Goal: give school users clear, tenant-isolated financial visibility.

- [ ] Build the Clerk-authenticated school dashboard.
- [ ] Show organization-only sales and order totals.
- [ ] Show earned, reserved, approved, processing, and paid amounts.
- [ ] Show payout history.
- [ ] Show connected-account onboarding and restriction status.
- [ ] Generate monthly statements from the ledger.
- [ ] Generate yearly summaries from the ledger.
- [ ] Provide authorized CSV downloads.
- [ ] Provide authorized PDF downloads.
- [ ] Explain Shopify, Printful, ShopGo, and Stripe responsibilities.
- [ ] Prevent school users from editing ledger or payout calculations.
- [ ] **Stage 12 Gate:** school users can access only their organization's records.

## Stage 13 - Manus And Agent Assistance

Goal: add auditable assistance without delegating final authority.

- [ ] Implement tracked Manus tasks.
- [ ] Implement tracked agent runs.
- [ ] Limit agents to preparation, recommendations, and drafts.
- [ ] Require human approval for publishing.
- [ ] Require human approval for permission decisions.
- [ ] Require human approval for payout decisions.
- [ ] Reconcile Shopify after every Manus-initiated change.
- [ ] Audit agent inputs, outputs, errors, and human decisions.
- [ ] **Stage 13 Gate:** agents cannot bypass server-side gates.

## Stage 14 - Security, Fidelity, And Release

Goal: verify the complete system before production exposure.

- [ ] Run lint.
- [ ] Run typecheck.
- [ ] Run unit and Convex tests.
- [ ] Run integration and component tests.
- [ ] Run the complete E2E suite.
- [ ] Run Codex Security on auth, uploads, webhooks, integrations, and payouts.
- [ ] Run CodeRabbit and resolve actionable findings.
- [ ] Compare implemented screens against Pencil exports on desktop.
- [ ] Compare implemented screens against Pencil exports on mobile.
- [ ] Verify keyboard navigation, contrast, labels, and reduced motion.
- [ ] Verify Shopify theme changes on an unpublished theme.
- [ ] Verify Vercel preview.
- [ ] Verify Convex preview deployment and environment isolation.
- [ ] Complete rollback procedures.
- [ ] Complete incident and webhook-replay procedures.
- [ ] Complete secret-rotation procedures.
- [ ] Obtain explicit approval before production deployment.
- [ ] Obtain explicit approval before live Shopify mutations.
- [ ] Obtain explicit approval before live Stripe money movement.
- [ ] **Stage 14 Gate:** every MVP release checkbox and evidence record is complete.

---

# MVP Release Checklist

- [ ] Super admin can create an organization, team, and storefront reference.
- [ ] Team collection URL points to `goteam.shop`.
- [ ] Clerk and Convex enforce tenant access.
- [ ] Brand kit and permission records exist.
- [ ] Product templates and starter packs work.
- [ ] Publish gate blocks missing permission, approval, Shopify, and Printful data.
- [ ] Shopify orders import idempotently.
- [ ] Printful costs and fulfillment data reconcile.
- [ ] Ledger entries are append-only and auditable.
- [ ] Payout batches are generated from ledger entries.
- [ ] Payout batches require reserve completion and super-admin approval.
- [ ] Stripe connected-account status and platform balance are checked.
- [ ] School portal shows reports and payout summaries.
- [ ] Audit logs exist for sensitive actions.
- [ ] Integration health and anomaly screens exist.
- [ ] CI and preview verification are green.
- [ ] Pencil visual-fidelity review is approved.
- [ ] Security review is complete.
- [ ] Product owner approves production deployment.

---

# Session Handoffs

Append entries. Never rewrite or remove earlier handoffs.

## Handoff Template

```md
### Session Handoff - YYYY-MM-DD
- Completed:
- Files changed:
- External changes:
- Verification run:
- Decisions made:
- Blockers:
- Exact next checkbox:
```

### Session Handoff - 2026-06-09

- Completed: Replaced the prior build document with this checkbox-driven runbook;
  removed the duplicated technical specification; added governance, environment,
  README, Git ignore, and decision-log files; initialized Git; installed and
  verified the Pencil, Clerk, and Shopify CLIs.
- Files changed: `Build.md`, `ShopGo.team Technical Specification v0.3.md`,
  `AGENTS.md`, `.env.example`, `.gitignore`, `README.md`, and
  `docs/DECISIONS.md`.
- External changes: Installed user-global packages under `/Users/tjoel/.local`:
  `@pencil.dev/cli@0.2.7`, `clerk@1.5.0`, and `@shopify/cli@4.1.0`.
  Published the local `main` branch to `https://github.com/tritecko/shopgoteam`.
- Verification run: Confirmed one technical-spec title, checked runbook status and
  stage checkboxes, scanned tracked source files for common secret patterns, and
  verified installed package versions and executable links.
- Decisions made: Used a user-owned npm prefix because writes to `/usr/local`
  were blocked; production provider mutations remain disabled.
- Blockers: Pencil CLI authentication is active, but the Pencil MCP editor is
  not connected to a running Pencil desktop app or IDE extension. Shopify CLI
  authorization for `shopgoteam.myshopify.com` must be completed in the product
  owner's Terminal because this managed session cannot write the Shopify CLI
  preferences directory.
- Exact next checkbox: Open Pencil desktop or IDE extension and confirm `.pen`
  editing works.

### Session Handoff - 2026-06-09 - Pencil Desktop Verification

- Completed: Verified the Pencil CLI stored session is active and verified the
  Pencil desktop MCP connection by reading the active
  `pencil-welcome-desktop.pen` editor document and schema.
- Files changed: `Build.md`.
- External changes: Pencil desktop is installed, running, authenticated, and
  connected to Codex through the desktop MCP server.
- Verification run: `pencil status` returned Active; Pencil
  `get_editor_state(include_schema: true)` returned the active desktop editor.
- Decisions made: Pencil desktop, not VS Code, is the required design host for
  this workspace.
- Blockers: Shopify CLI has no saved authorization for the permanent store
  domain `2gatxz-fd.myshopify.com`.
- Exact next checkbox: Connect Shopify CLI to the existing store with the
  approved scopes.

### Session Handoff - 2026-06-09 - Stage 0 Completion

- Completed: Verified Pencil desktop connectivity, completed Shopify CLI OAuth
  for the permanent store domain `2gatxz-fd.myshopify.com`, and closed the
  Stage 0 gate.
- Files changed: `Build.md` and `docs/DECISIONS.md`.
- External changes: Shopify CLI Connector App authorization was approved for the
  existing store using the required scopes.
- Verification run: Shopify reported `Authentication succeeded`; a read-only
  `shopify store execute --store 2gatxz-fd.myshopify.com --query
  'query { shop { name id } }'` invocation returned `ShopGoTeam` and
  `gid://shopify/Shop/67069280343`.
- Decisions made: The permanent Shopify authentication domain is
  `2gatxz-fd.myshopify.com`; `shopgoteam` remains the admin handle and
  `goteam.shop` remains the customer-facing domain.
- Blockers: None.
- Exact next checkbox: Create the ShopGo Pencil design document.

### Session Handoff - 2026-06-09 - GitHub Sync And Stage 1 Handoff

- Completed: Confirmed every Stage 0 task and the Stage 0 gate are checked;
  synchronized the verified Stage 0 runbook and decision log to GitHub; prepared
  the workspace for a new agent session beginning Stage 1.
- Files changed: `Build.md`.
- External changes: GitHub `main` for `tritecko/shopgoteam` was updated through
  commit `8155b0f`.
- Verification run: `git status --short --branch` returned
  `## main...origin/main` with no pending files before this handoff update;
  `git log -3 --oneline` showed `8155b0f docs: complete Stage 0 setup` as the
  current commit.
- Decisions made: Stage 1 must begin in Pencil and produce the approved `.pen`
  design source before application scaffolding or production UI implementation.
- Blockers: None.
- Exact next checkbox: Create the ShopGo Pencil design document.

### Session Handoff - 2026-06-10 - Pencil Document Activation Blocker

- Completed: Re-read the required project documents, confirmed the exact Stage 1
  starting checkbox, verified the live Pencil desktop MCP connection, and
  inspected the supplied Kraken references. No Stage 1 checkbox was completed.
- Files changed: `Build.md`. An invalid zero-byte
  `design/shopgo-team.pen` attempt and empty artifact directories were removed.
- External changes: None. No Shopify or other production provider mutation was
  performed.
- Verification run: Pencil `get_editor_state(include_schema: true)` returned the
  active `pencil-welcome-desktop.pen` editor. Pencil CLI `0.2.7` authentication
  returned Active. `pencil interactive --app desktop --in
  design/shopgo-team.pen` connected to desktop but did not activate the
  workspace document; both desktop-connected and headless `save()` calls
  reported success while the output remained zero bytes.
- Decisions made: Do not continue Stage 1 in headless Pencil. The canonical
  workspace file must be genuinely opened and saved by Pencil desktop before
  production design editing resumes.
- Blockers: Create or open a new Pencil desktop document and save it as
  `/Users/tjoel/Documents/Dev/shopgoteam-app/design/shopgo-team.pen`.
- Exact next checkbox: Create the ShopGo Pencil design document.

### Session Handoff - 2026-06-10 - Stage 1 Primary Screens And Audit Blocker

- Completed: Created the canonical live Pencil design document, foundations,
  reusable operating system, and desktop/mobile designs for Admin Dashboard,
  Team Detail, Product Publish Gate, Finance Command Center, Payout Batch
  Detail, School Dashboard, and Integration Health.
- Files changed: `Build.md`,
  `docs/design/shopify-storefront-audit.md`, and the live Pencil document at
  `design/shopgo-team.pen`. Temporary Pencil files under `design/` predate this
  handoff and were not used as production design sources.
- External changes: None. Public storefront inspection and Shopify CLI attempts
  were read-only. No Shopify, Stripe, Clerk, Convex, Printful, Manus, Vercel, or
  GitHub production state was mutated.
- Verification run: Pencil desktop MCP confirmed the active canonical filename.
  Foundations and reusable components pass layout and screenshot review. All
  seven primary desktop/mobile pairs return no problems from
  `snapshot_layout(problemsOnly: true)` and pass screenshot review. The public
  storefront at `https://shopgo.team` loaded as a Shopify placeholder with
  default navigation, eight repeated placeholder products at `$19.99`, no
  detected product imagery, and generic email/footer content.
- Decisions made: The product owner corrected the public audit URL to
  `https://shopgo.team`; earlier checks of `goteam.shop` and `goshop.team` are
  discarded. Kraken Web 37 and Kraken Web 62 informed the detached navigation,
  pale atmospheric canvas, large financial hierarchy, low-density operational
  bands, compact controls, and right-side action docks without copying Kraken
  branding.
- Blockers: Shopify CLI `4.1.0` stored authentication refresh returned HTTP 401
  and requires read-only reauthentication. Pencil Desktop must also persist the
  current live document with `Cmd+S`; the on-disk file timestamp still reflects
  the initial save.
- Exact next checkbox: Audit `shopgo.team` read-only.

### Session Handoff - 2026-06-13 - Stage 1 Complete And Published

- Completed: Finished the canonical Pencil design system and verified all
  Stage 1 tasks. Added the serialized Pencil token JSON, semantic export
  manifest, 18 desktop/mobile 2x PNG exports, acceptance record, and the
  editorial operations redesign specification. Product-owner approval was
  recorded and the Stage 1 gate was checked.
- Files changed: `Build.md`, `design.md`, `design/shopgo-team.pen`,
  `design/exports/screens/`, `design/exports/tokens/shopgo-design-tokens.json`,
  `docs/design/STAGE-1-ACCEPTANCE.md`,
  `docs/design/shopify-storefront-audit.md`, and
  `docs/superpowers/specs/2026-06-12-stage-1-editorial-operations-redesign.md`.
  The obsolete local draft `design/goshopteam.pen` was not added to Git.
- Pencil changes: Replaced heavy panel outlines with tonal surfaces, spacing,
  and restrained soft shadows; introduced the Sora/Inter type system and
  operational horizon; removed permanent right-side action rails; added inline
  action stages; and added a separate School Orders and Delivery screen.
- External changes: Pushed branch `codex/stage-1-pencil-design` and opened draft
  pull request `https://github.com/tritecko/shopgoteam/pull/1`. The
  `https://shopgo.team` audit remained read-only. No Shopify, Stripe, Clerk,
  Convex, Printful, Manus, or Vercel production state was mutated.
- Verification run: Pencil Desktop MCP confirmed
  `design/shopgo-team.pen` as the active canonical document. All 18 accepted
  frames returned no problems from
  `snapshot_layout(problemsOnly: true)` and representative desktop/mobile
  screenshots passed visual review. The manifest contains 18 entries and every
  referenced PNG exists. Desktop exports are `2880 x 2048`; mobile exports are
  `780 x 1688`. Both JSON files parse successfully, `git diff --check` passed,
  and the scoped secret-pattern scan returned no findings.
- Decisions made: Pencil remains the sole production design source. Admin users
  retain technical Printful health and mapping details; school users receive
  only order status, estimated delivery, tracking, exceptions, and secondary
  provider attribution. Stage 2 is ready but was not started.
- Blockers: None.
- Exact next checkbox: Scaffold Next.js App Router with pnpm and strict
  TypeScript.
