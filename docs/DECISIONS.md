# ShopGo.team Decision Log

This file records decisions that materially affect architecture, finance,
security, integrations, or product scope.

## 2026-06-09 - Public Storefront And Portal Split

**Decision:** Shopify at `goteam.shop` renders the public catalog, cart,
checkout, payment experience, and buyer order record. Next.js at `shopgo.team`
renders the authenticated ShopGo admin and school portals.

**Reason:** The Shopify store already exists and should remain the commerce
surface, while ShopGo needs a purpose-built operational and reporting portal.

## 2026-06-09 - U.S.-First MVP

**Decision:** The MVP launches in the United States with USD-only finance.

**Reason:** This limits currency, reporting, connected-account, and payout
complexity during the first release.

## 2026-06-09 - Convex Authorization

**Decision:** Clerk authenticates identities and manages organization lifecycle.
Convex mirrors those records and authorizes every business operation.

**Reason:** Business permissions, tenant boundaries, publishing gates, and payout
controls must be enforced beside the data and cannot rely on client claims.

## 2026-06-09 - Stripe Funding Model

**Decision:** Shopify revenue does not automatically fund Stripe. ShopGo must
fund its Stripe platform balance before approved transfers to connected accounts.
Live transfers remain disabled until this operating process is verified.

**Reason:** Shopify processes buyer payments outside Stripe Connect. Transfers
fail when the Stripe platform balance lacks available funds.

## 2026-06-09 - Pencil As Design Source

**Decision:** Pencil is the production UI design source for admin, school, and
approved Shopify theme changes.

**Reason:** Design approval must precede implementation and provide stable tokens,
responsive behavior, and visual QA references.

