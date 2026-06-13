# ShopGo.team Storefront Audit

Date: 2026-06-10
Mode: Read-only
Production mutations: None

## Canonical Domains

- Public storefront: `https://shopgo.team`
- Permanent Shopify domain: `2gatxz-fd.myshopify.com`
- Store name from the last verified authenticated query: `ShopGoTeam`
- Shopify shop ID from the last verified authenticated query:
  `gid://shopify/Shop/67069280343`

Earlier references to `goteam.shop` and `goshop.team` were user-corrected during
this audit and are not storefront evidence.

## Public Storefront Findings

The public storefront loaded successfully at `https://shopgo.team`.

- Header brand: `ShopGoTeam`
- Announcement: `Welcome to our store`
- Primary navigation: Home, Catalog, Contact
- Hero copy: `Browse our latest products`
- Primary hero action: `Shop all`
- Product grid: eight repeated placeholder cards titled `Product title`
- Product price shown on every placeholder card: `$19.99`
- Product imagery: none detected in the rendered document
- Email capture: generic `Join our email list` content
- Footer: default Shopify attribution and policy control

## Audit Interpretation

The storefront is a live Shopify theme, but its visible content is still
placeholder material. The public experience does not yet communicate the
school-team fundraising model, team identity, fulfillment responsibility, or
real product catalog.

This evidence justifies a Pencil-only storefront proposal focused on:

- team identity and school context;
- fundraiser value and school benefit;
- real product imagery and product metadata;
- team-scoped navigation and collections;
- clear Shopify checkout and Printful fulfillment responsibilities;
- mobile-first merchandising and trust content.

## Authenticated Shopify Inventory

Shopify CLI `4.1.0` completed read-only Admin GraphQL queries against
`2gatxz-fd.myshopify.com`. Every command omitted `--allow-mutations`.

### Shop And Domains

- Shop: `ShopGoTeam`
- Shop ID: `gid://shopify/Shop/67069280343`
- Permanent domain: `2gatxz-fd.myshopify.com`
- Primary domain: `shopgo.team`
- SSL-enabled domains:
  - `2gatxz-fd.myshopify.com`
  - `shopgoteam.myshopify.com`
  - `shopgo.team`
  - `www.shopgo.team`

### Themes

- `Horizon`: main theme
- `Rise`: unpublished

### Navigation

The authenticated `menus` query was denied under the approved read scopes.
Public rendering provides the verified visible navigation evidence: Home,
Catalog, and Contact.

### Collections And Products

- One collection: `Home page` (`frontpage`)
- Products in collection: 0
- Actual Shopify products: 0
- Product featured imagery: none, because no products exist

The eight storefront cards are theme placeholder content rather than Shopify
product records.

### Apps, Webhooks, And Metafields

- App installation inventory: access denied under the approved read scopes
- Webhook subscriptions: none
- Shop metafield definitions: none

## Audit Conclusion

The current production storefront is structurally live but contains no real
catalog data. A Pencil proposal is justified for a team storefront home and
mobile experience, but implementation must remain blocked until Stage 2+ and
must not mutate the live Horizon theme without explicit product-owner approval.
