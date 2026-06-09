# ShopGo.team Agent Instructions

## Required Reading

Before making changes, read:

- `PRD.md`
- `ShopGo.team Technical Specification v0.3.md`
- `design.md`
- `Build.md`
- the latest handoff in `Build.md`

## Execution Contract

- Continue from the exact next unchecked task in `Build.md`.
- Update the `Build.md` status block and append a handoff before ending.
- Check tasks only after verification evidence passes.
- Preserve prior handoffs and completed checkboxes.
- Keep one build stage active at a time.
- Use test-driven development for behavior changes.
- Never commit secrets.
- Never bypass Convex authorization in server functions.
- Never mutate a production provider without explicit product-owner approval.

## Architecture Rules

- Convex owns ShopGo business state, authorization, workflows, ledger, reports,
  audits, and integration state.
- Clerk owns identity, sessions, invitations, and organization lifecycle.
- Shopify at `goteam.shop` owns the public storefront, checkout, payment, and
  buyer order record.
- Printful owns POD mapping, production, fulfillment, packing, and shipping.
- Stripe Connect is a post-sale payout rail only.
- Stripe transfers require a funded ShopGo platform balance.
- Pencil is the production UI design source.
- Ledger entries are append-only.
- Products cannot publish without complete approvals and verified fulfillment
  mappings.
- Agents and Manus cannot approve publishing, permission, or payouts.

## Completion Checks

When the application exists, run:

```bash
pnpm lint
pnpm typecheck
pnpm test
pnpm build
```

Use targeted Playwright tests for UI workflows. Use Codex Security for auth,
uploads, webhooks, integrations, and payouts.

