# ShopGo.team

ShopGo.team is a fundraising operations platform for school sports merchandise.

Supporters browse and buy at [goteam.shop](https://goteam.shop). Shopify owns
the public storefront and checkout, Printful fulfills products, Convex operates
the ShopGo workflows and ledger, Clerk authenticates admin and school users, and
Stripe Connect sends approved post-sale school payouts.

## Start Here

Read these documents in order:

1. `PRD.md`
2. `ShopGo.team Technical Specification v0.3.md`
3. `design.md`
4. `Build.md`
5. `AGENTS.md`

`Build.md` is the execution runbook. Continue from its exact next unchecked task
and append a session handoff before ending work.

## Safety

- Do not commit secrets.
- Do not mutate the production Shopify store without explicit approval.
- Do not enable live Stripe transfers without explicit approval.
- Do not bypass Convex authorization.
- Do not publish products without permissions, approvals, and verified Printful
  mappings.

## Current State

The repository is in Stage 0: prerequisites and governance. Application
scaffolding begins in Stage 2 after Pencil designs are approved.

## Installed CLI Location

Stage 0 installs user-global command-line tools under:

```txt
/Users/tjoel/.local/bin
```

If that directory is not already on the shell path:

```bash
export PATH="$HOME/.local/bin:$PATH"
```
