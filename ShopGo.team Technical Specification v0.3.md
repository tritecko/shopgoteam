# ShopGo.team Technical Specification v0.3

## 1. Product vision translation

**ShopGo.team is a fundraising merch operations platform for high school sports teams.**

The public buyer experience is simple:

A parent, student, alumni supporter, or fan visits a team-specific merch page such as:

```
shopgo.team/crestwood-ravens
```

They browse approved team merchandise and purchase through a **Shopify checkout experience**. Printful powers the product catalog sync, print-on-demand inventory logic, fulfillment, packing, and shipping behind the scenes. After payment, Printful receives and fulfills the synced order items and ships them to the customer.

The admin experience is the true product:

ShopGo gives you a centralized dashboard to onboard teams, manage school permissions, generate or upload mascot/product artwork, create Shopify/Printful products, organize them into team storefronts, monitor orders, calculate fundraiser earnings, approve payouts, and provide school finance admins with clean monthly/yearly reporting.

The full operational model is:

```
Buyers purchase through a Shopify checkout experience.
Printful powers product sync, POD fulfillment, packing, and shipping.
Stripe Connect powers post-sale school payouts after Convex calculates fundraiser earnings.
```

## 2. Core architecture decision

Use:

```
Next.js + Convex + Clerk + Shopify + Printful + Stripe Connect + Manus
```

With the following responsibilities:

| System | Responsibility |
| --- | --- |
| Next.js | ShopGo admin dashboard, school dashboard, optional fallback storefront renderer |
| Convex | Database, backend functions, realtime dashboard, job state, ledger, file metadata, audit logs |
| Clerk | User auth, organizations/roles, invited school admins |
| Shopify | Buyer-facing checkout experience, customer orders, taxes at checkout, payment capture, storefront commerce records |
| Printful | Product catalog sync, POD inventory/variant mapping, mockups, print files, fulfillment, packing, shipping |
| Stripe Connect | Post-sale school/booster payout rail after ShopGo calculates fundraiser earnings |
| Manus | Shopify/storefront/catalog operator and campaign assistant |
| AI agents | Intake, product setup, design briefing, reconciliation, reporting, ops monitoring |

Manus can be used to assist with storefront/page creation, collection organization, product copy, campaigns, and Shopify operations. However, Manus should not become the system of record. Convex remains the operating brain.

## 3. System-of-record rules

These rules prevent chaos later.

### 3.1 Convex is the operating system

Convex owns:

* schools
* teams
* team slugs
* users and roles
* approval records
* product pipeline state
* asset metadata
* integration mappings
* Shopify IDs
* Printful IDs
* Stripe connected account IDs
* fundraiser ledger entries
* payout batches
* audit logs
* agent runs

Convex HTTP actions should receive webhooks from Shopify, Stripe, Printful, Clerk, and optionally Manus.

### 3.2 Shopify is the checkout and order source of truth

Shopify owns:

* buyer-facing cart and checkout experience
* payment capture
* checkout taxes
* customer order records
* customer-facing order confirmation
* Shopify product records
* Shopify collections
* Shopify refund/order events

The buyer-facing flow is:

```
Team storefront → product page → Shopify cart/checkout → payment confirmation
```

ShopGo should not describe Stripe as powering customer checkout in the MVP. Stripe is downstream of checkout.

### 3.3 Printful is the POD fulfillment source of truth

Printful owns:

* blank product catalog
* product listing options that feed the merch pipeline
* variant availability
* product sync/mapping
* print files
* mockup generation
* production
* packing
* shipping
* fulfillment status

The operational backend flow is:

```
Shopify order → synced Printful product/variant → Printful fulfillment → Printful ships to customer
```

Every published Shopify product/variant must have a verified Printful sync mapping before the storefront can go live.

### 3.4 Stripe Connect is the post-sale payout rail

Stripe Connect should not replace Shopify checkout in the MVP.

Instead:

```
Shopify collects customer payment
→ Convex calculates fundraiser earnings
→ Stripe Connect transfers approved school/booster payout
```

Stripe Connect owns:

* connected school/booster payout accounts
* onboarding/KYC flow
* payout eligibility status
* post-sale transfers
* connected-account tax/profile tools where supported

Stripe is still very much part of the platform — just not the buyer checkout layer.

### 3.5 Manus is an operator, not authority

Manus should be allowed to:

* generate storefronts
* update landing page copy
* organize Shopify collections
* create/edit product descriptions
* create discounts/campaigns
* analyze Shopify sales for campaign ideas

Manus should not be trusted as the authority for:

* payout math
* revenue-share rules
* school permission records
* final approval status
* financial ledger
* tax summaries
* audit logs

---

# 4. Primary user types

## 4.1 Super admin

This is you.

Capabilities:

* create/edit schools
* create/edit teams
* manage all team storefronts
* approve/reject designs
* publish/unpublish products
* override pricing
* override revenue share
* freeze payouts
* approve payout batches
* invite school admins
* view all financial data
* view integration health
* view agent run logs
* manually retry failed operations

## 4.2 ShopGo operator

Future staff or contractor.

Capabilities:

* create teams
* upload assets
* manage product pipeline
* request Manus tasks
* prepare payout batches
* cannot approve final payouts
* cannot change global revenue-share rules without admin permission

## 4.3 School financial admin

Bookkeeper, treasurer, booster club finance person.

Capabilities:

* view approved team storefronts
* view sales summaries
* download monthly/yearly reports
* view payout history
* complete Stripe onboarding
* update payout recipient information through Stripe
* download fundraiser statements
* cannot change products/prices unless explicitly allowed

## 4.4 Athletic director / coach / booster contact

Capabilities:

* approve team branding
* approve designs
* view live store link
* view sales performance
* invite optional viewers
* cannot see sensitive customer PII
* cannot trigger payouts

## 4.5 Buyer

Capabilities:

* browse a team storefront
* purchase through Shopify checkout
* receive Shopify/Printful fulfillment updates

---

# 5. Public URL model

Each team gets a canonical slug.

Example:

```
shopgo.team/crestwood-ravens
shopgo.team/central-tigers-football
shopgo.team/ridgeview-hawks-volleyball
```

## Required behavior

`teamSlug` must be unique globally.

Preferred slug format:

```
{school-short-name}-{mascot}
```

Optional sport-specific variant:

```
{school-short-name}-{mascot}-{sport}
```

## Slug collision rules

If `crestwood-ravens` exists:

```
crestwood-ravens-2
crestwood-ravens-football
crestwood-ravens-al
```

depending on your chosen collision policy.

## Storefront rendering options

### Option A — Manus-served storefront

Manus builds/serves the frontend storefront and hands checkout to Shopify.

Pros:

* faster visual build
* strong for rapid iteration
* chat-based store updates

Risks:

* unclear long-term control over many dynamic slugs
* dependency on Manus serving model
* possible limitations around programmatic multi-team routing

### Option B — Next.js-rendered storefront

ShopGo renders `/[teamSlug]` itself using Convex + Shopify Storefront API/Admin-synced product data.

Pros:

* full control
* easier predictable routing
* better SEO/system ownership
* less vendor dependency

Risks:

* more engineering work
* you must build merchandising UI

### Recommendation

Build the data model so **either option works**.

Initial MVP can use Manus heavily, but Next.js should still be capable of rendering a basic fallback storefront from Convex/Shopify data.

---

# 6. Core workflows

## 6.1 Team onboarding workflow

1. Admin creates school.
2. Admin creates team.
3. System generates slug.
4. Admin enters mascot, colors, school website, sport, level, location.
5. Admin uploads or generates brand assets.
6. School permission record is created.
7. Revenue-share rule is assigned.
8. Stripe connected account invite is generated, if payout recipient is known.
9. Product starter pack is generated.
10. Products enter design/mockup/approval pipeline.

## 6.2 Product creation workflow

1. Choose Printful blank product.
2. Create/upload design asset.
3. Create Printful sync product or variant mapping.
4. Push/sync product into Shopify.
5. Assign Shopify product to team collection.
6. Verify Shopify variant ↔ Printful variant mapping.
7. Generate or retrieve Printful mockups.
8. Store mockups in assets.
9. Create draft product records in Convex.
10. Submit products for admin approval.
11. Submit to school approval if required.
12. Publish to the team storefront.
13. Buyer purchases through Shopify checkout.
14. Printful fulfills after order sync.

## 6.3 Buyer workflow

1. Buyer visits team slug.
2. Buyer views team collection/products.
3. Buyer clicks product.
4. Buyer purchases through a Shopify checkout experience.
5. Shopify records the order/payment.
6. Printful receives and fulfills the synced POD items.
7. Printful prints, packs, and ships the order to the customer.
8. Convex receives order/fulfillment updates.
9. Ledger entries are generated.
10. School dashboard updates.

## 6.4 Payout workflow

1. Shopify checkout processes buyer purchases.
2. Shopify order webhooks notify Convex.
3. Printful fulfillment and cost data are synced into Convex.
4. Convex creates immutable ledger entries.
5. Monthly payout batch is generated.
6. Refund reserve window is applied.
7. Admin reviews payout batch.
8. Admin approves payout batch.
9. Stripe Connect transfer is triggered.
10. School finance admin receives payout and can download statement.

## 6.5 Reporting workflow

School finance admin can download:

* monthly fundraiser statement
* yearly fundraiser summary
* payout history CSV
* order-line summary CSV
* adjustment/refund report
* product-level sales summary
* team-level campaign summary

Stripe Express can help connected accounts with payout account management and tax profile workflows where supported, but ShopGo still needs its own fundraiser reporting because schools need context by team, campaign, collection, and payout period.

---

# 7. Database schema

This assumes Convex tables. Field names are TypeScript-style.

## 7.1 `users`

Stores every authenticated app user.

```
users: {
  clerkUserId: string
  email: string
  name?: string
  avatarUrl?: string
  globalRole: "super_admin" | "operator" | "support" | "none"
  status: "active" | "invited" | "suspended"
  defaultOrgId?: Id<"organizations">
  createdAt: number
  updatedAt: number
  lastLoginAt?: number
}
```

Indexes:

```
by_clerkUserId
by_email
by_globalRole
```

## 7.2 `organizations`

Represents schools, booster clubs, districts, or athletic departments.

```
organizations: {
  name: string
  normalizedName: string
  type: "school" | "booster_club" | "district" | "athletic_department" | "other"
  country: "US" | "CA"
  stateProvince?: string
  city?: string
  addressLine1?: string
  postalCode?: string
  website?: string
  status: "prospect" | "onboarding" | "active" | "paused" | "archived"
  defaultRevenueShareRuleId?: Id<"revenueShareRules">
  stripeConnectedAccountId?: string
  stripeConnectedAccountStatus?: "not_started" | "pending" | "active" | "restricted" | "rejected"
  primaryContactId?: Id<"contacts">
  notes?: string
  createdBy: Id<"users">
  createdAt: number
  updatedAt: number
}
```

Indexes:

```
by_status
by_normalizedName
by_stripeConnectedAccountId
```

## 7.3 `organizationMembers`

Maps users to schools/orgs.

```
organizationMembers: {
  organizationId: Id<"organizations">
  userId: Id<"users">
  role: "school_finance_admin" | "athletic_director" | "coach" | "booster_admin" | "viewer"
  permissions: string[]
  status: "invited" | "active" | "removed"
  invitedBy?: Id<"users">
  createdAt: number
  updatedAt: number
}
```

Indexes:

```
by_organizationId
by_userId
by_organizationId_userId
```

## 7.4 `contacts`

Non-login contacts.

```
contacts: {
  organizationId: Id<"organizations">
  name: string
  email?: string
  phone?: string
  title?: string
  contactType: "athletic_director" | "coach" | "booster" | "finance" | "principal" | "other"
  source: "manual" | "school_website" | "directory" | "import" | "other"
  permissionStatus: "unknown" | "requested" | "granted" | "denied"
  notes?: string
  createdAt: number
  updatedAt: number
}
```

## 7.5 `teams`

The core storefront unit.

```
teams: {
  organizationId: Id<"organizations">
  displayName: string
  teamSlug: string
  schoolName: string
  mascot?: string
  sport?: string
  level?: "varsity" | "junior_varsity" | "freshman" | "club" | "all_school" | "other"
  gender?: "boys" | "girls" | "coed" | "not_applicable"
  season?: "fall" | "winter" | "spring" | "summer" | "year_round"
  primaryColor?: string
  secondaryColor?: string
  accentColor?: string
  status: "draft" | "onboarding" | "designing" | "approval_needed" | "active" | "paused" | "archived"
  storefrontId?: Id<"teamStorefronts">
  brandKitId?: Id<"brandKits">
  createdBy: Id<"users">
  createdAt: number
  updatedAt: number
}
```

Indexes:

```
by_organizationId
by_teamSlug
by_status
by_sport
```

## 7.6 `teamStorefronts`

Maps each team to public storefront and Shopify/Manus records.

```
teamStorefronts: {
  teamId: Id<"teams">
  organizationId: Id<"organizations">
  slug: string
  publicUrl: string
  renderer: "manus" | "nextjs" | "shopify_theme" | "hybrid"
  status: "draft" | "building" | "ready_for_review" | "published" | "paused" | "error"
  shopifyCollectionId?: string
  shopifyCollectionHandle?: string
  shopifyPageId?: string
  shopifyMenuHandle?: string
  manusStorefrontId?: string
  manusSessionId?: string
  heroAssetId?: Id<"assets">
  seoTitle?: string
  seoDescription?: string
  publishedAt?: number
  lastSyncedAt?: number
  createdAt: number
  updatedAt: number
}
```

Indexes:

```
by_slug
by_teamId
by_status
by_shopifyCollectionId
```

## 7.7 `brandKits`

School/team identity package.

```
brandKits: {
  organizationId: Id<"organizations">
  teamId?: Id<"teams">
  primaryColor?: string
  secondaryColor?: string
  accentColor?: string
  logoAssetId?: Id<"assets">
  mascotAssetId?: Id<"assets">
  fontPreference?: string
  visualStyle: "classic_athletic" | "vintage_collegiate" | "bold_modern" | "mascot_cartoon" | "minimal" | "custom"
  styleNotes?: string
  restrictedTerms?: string[]
  approvedBy?: Id<"users">
  approvedAt?: number
  status: "draft" | "needs_review" | "approved" | "rejected"
  createdAt: number
  updatedAt: number
}
```

## 7.8 `permissions`

Tracks school authorization to use name/mascot/logo/colors.

```
permissions: {
  organizationId: Id<"organizations">
  teamId?: Id<"teams">
  permissionType: "school_name" | "mascot" | "logo" | "colors" | "team_name" | "fundraiser" | "booster_authorization"
  status: "not_requested" | "requested" | "granted" | "denied" | "expired"
  grantedByName?: string
  grantedByEmail?: string
  documentAssetId?: Id<"assets">
  validFrom?: number
  validUntil?: number
  notes?: string
  createdAt: number
  updatedAt: number
}
```

This is not optional. It protects the business.

## 7.9 `assets`

Metadata for files stored in Convex storage or external object storage.

```
assets: {
  ownerType: "organization" | "team" | "product" | "designJob" | "payoutBatch" | "report" | "user"
  ownerId: string
  storageProvider: "convex" | "s3" | "shopify" | "printful" | "external_url"
  storageId?: string
  externalUrl?: string
  filename: string
  mimeType: string
  fileSizeBytes?: number
  assetType:
    | "school_logo"
    | "mascot_source"
    | "ai_design"
    | "print_file"
    | "mockup"
    | "hero_image"
    | "approval_document"
    | "monthly_statement_pdf"
    | "csv_export"
    | "receipt"
  status: "uploaded" | "processing" | "approved" | "rejected" | "archived"
  metadata?: any
  checksum?: string
  createdBy?: Id<"users">
  createdAt: number
  updatedAt: number
}
```

## 7.10 `productTemplates`

Internal template records for your starter pack.

```
productTemplates: {
  name: string
  category: "t_shirt" | "hoodie" | "crewneck" | "hat" | "long_sleeve" | "sweatpants" | "other"
  fulfillmentProvider: "printful"
  printfulCatalogProductId: string
  defaultPlacement: "front" | "back" | "left_chest" | "sleeve" | "embroidered_front"
  allowedPlacements: string[]
  defaultVariantIds: string[]
  allowedColors: string[]
  allowedSizes: string[]
  baseCostCents?: number
  defaultRetailPriceCents?: number
  defaultMarginCents?: number
  active: boolean
  createdAt: number
  updatedAt: number
}
```

## 7.11 `products`

ShopGo product intent/state.

```
products: {
  organizationId: Id<"organizations">
  teamId: Id<"teams">
  storefrontId: Id<"teamStorefronts">
  productTemplateId: Id<"productTemplates">
  title: string
  description?: string
  status:
    | "draft"
    | "designing"
    | "mockup_requested"
    | "mockup_ready"
    | "admin_review"
    | "school_review"
    | "approved"
    | "published"
    | "paused"
    | "archived"
    | "error"
  designAssetId?: Id<"assets">
  printFileAssetIds?: Id<"assets">[]
  mockupAssetIds?: Id<"assets">[]
  shopifyProductId?: string
  shopifyHandle?: string
  printfulSyncProductId?: string
  retailPriceCents: number
  estimatedCostCents?: number
  currency: "USD" | "CAD"
  revenueShareRuleId: Id<"revenueShareRules">
  tags: string[]
  createdBy: Id<"users">
  approvedByAdmin?: Id<"users">
  approvedBySchool?: Id<"users">
  publishedAt?: number
  createdAt: number
  updatedAt: number
}
```

Indexes:

```
by_teamId
by_status
by_shopifyProductId
by_printfulSyncProductId
```

## 7.12 `productVariants`

```
productVariants: {
  productId: Id<"products">
  size: string
  color: string
  sku: string
  shopifyVariantId?: string
  printfulVariantId?: string
  printfulSyncVariantId?: string
  retailPriceCents: number
  estimatedCostCents?: number
  mappingStatus: "unmapped" | "mapped" | "verified" | "error"
  active: boolean
  createdAt: number
  updatedAt: number
}
```

## 7.13 `orders`

Convex snapshot of Shopify order data.

```
orders: {
  shopifyOrderId: string
  shopifyOrderName?: string
  organizationId: Id<"organizations">
  teamId?: Id<"teams">
  storefrontId?: Id<"teamStorefronts">
  currency: "USD" | "CAD"
  subtotalCents: number
  discountCents: number
  taxCents: number
  shippingCents: number
  totalCents: number
  financialStatus: "pending" | "paid" | "partially_refunded" | "refunded" | "voided" | "unknown"
  fulfillmentStatus: "unfulfilled" | "partial" | "fulfilled" | "cancelled" | "unknown"
  customerEmailHash?: string
  sourceCreatedAt: number
  importedAt: number
  updatedAt: number
}
```

Indexes:

```
by_shopifyOrderId
by_teamId
by_organizationId
by_sourceCreatedAt
```

## 7.14 `orderItems`

```
orderItems: {
  orderId: Id<"orders">
  shopifyLineItemId: string
  productId?: Id<"products">
  productVariantId?: Id<"productVariants">
  printfulSyncVariantId?: string
  teamId: Id<"teams">
  quantity: number
  title: string
  sku?: string
  unitPriceCents: number
  discountCents: number
  estimatedUnitCostCents?: number
  actualUnitCostCents?: number
  grossRevenueCents: number
  netRevenueCents?: number
  fulfillmentProvider: "printful"
  fulfillmentStatus?: "pending" | "submitted" | "in_production" | "shipped" | "delivered" | "cancelled" | "unknown"
  createdAt: number
}
```

## 7.15 `revenueShareRules`

```
revenueShareRules: {
  organizationId?: Id<"organizations">
  teamId?: Id<"teams">
  name: string
  calculationBase: "net_merch_profit" | "gross_merch_sales" | "fixed_per_item" | "custom"
  schoolSharePercent?: number
  fixedAmountPerItemCents?: number
  includeShipping: boolean
  includeTaxes: boolean
  deductPaymentFees: boolean
  deductFulfillmentCosts: boolean
  refundPolicy: "reverse_commission_on_refund" | "no_reversal_after_payout" | "manual_review"
  effectiveFrom: number
  effectiveUntil?: number
  status: "active" | "inactive" | "superseded"
  createdBy: Id<"users">
  createdAt: number
  updatedAt: number
}
```

Default recommendation:

```
school share = percentage of net merch profit
```

Where:

```
net merch profit = item revenue - discounts - refunds - Printful costs - attributable payment fees - manual adjustments
```

Taxes should not be treated as fundraiser earnings.

## 7.16 `ledgerEntries`

Immutable finance engine.

```
ledgerEntries: {
  organizationId: Id<"organizations">
  teamId?: Id<"teams">
  orderId?: Id<"orders">
  orderItemId?: Id<"orderItems">
  payoutBatchId?: Id<"payoutBatches">
  type:
    | "gross_sale"
    | "discount"
    | "refund"
    | "tax_collected"
    | "shipping_collected"
    | "shopify_fee"
    | "payment_processing_fee"
    | "printful_cost"
    | "manual_adjustment"
    | "school_commission"
    | "platform_margin"
    | "payout_transfer"
    | "payout_reversal"
    | "reserve_hold"
    | "reserve_release"
  amountCents: number
  currency: "USD" | "CAD"
  direction: "credit" | "debit"
  source: "shopify" | "printful" | "stripe" | "manual" | "system"
  sourceId?: string
  memo?: string
  immutable: true
  createdBy?: Id<"users">
  createdAt: number
}
```

Rules:

* Never update ledger entries.
* Corrections require new reversing entries.
* Every payout is traceable to ledger entries.
* Reports are generated from ledger, not from recalculating raw orders every time.

## 7.17 `payoutBatches`

```
payoutBatches: {
  organizationId: Id<"organizations">
  periodStart: number
  periodEnd: number
  reserveUntil: number
  currency: "USD" | "CAD"
  grossSalesCents: number
  refundsCents: number
  fulfillmentCostsCents: number
  feesCents: number
  netProfitCents: number
  schoolShareCents: number
  platformShareCents: number
  status: "draft" | "pending_review" | "approved" | "processing" | "paid" | "failed" | "cancelled"
  stripeTransferId?: string
  statementAssetId?: Id<"assets">
  approvedBy?: Id<"users">
  approvedAt?: number
  paidAt?: number
  error?: string
  createdAt: number
  updatedAt: number
}
```

## 7.18 `stripeConnectedAccounts`

```
stripeConnectedAccounts: {
  organizationId: Id<"organizations">
  stripeAccountId: string
  accountMode: "express" | "standard" | "custom" | "accounts_v2"
  country: "US" | "CA"
  status: "created" | "onboarding" | "active" | "restricted" | "disabled"
  chargesEnabled?: boolean
  payoutsEnabled?: boolean
  detailsSubmitted?: boolean
  requirementsDue?: string[]
  latestOnboardingLink?: string
  lastWebhookAt?: number
  createdAt: number
  updatedAt: number
}
```

## 7.19 `reports`

```
reports: {
  organizationId: Id<"organizations">
  teamId?: Id<"teams">
  payoutBatchId?: Id<"payoutBatches">
  reportType: "monthly_statement" | "yearly_summary" | "order_export" | "payout_receipt" | "tax_support_summary"
  periodStart: number
  periodEnd: number
  assetId: Id<"assets">
  status: "generating" | "ready" | "failed"
  createdBy?: Id<"users">
  createdAt: number
}
```

## 7.20 `integrationAccounts`

```
integrationAccounts: {
  provider: "shopify" | "printful" | "stripe" | "manus" | "openai" | "image_model"
  organizationId?: Id<"organizations">
  teamId?: Id<"teams">
  status: "active" | "needs_auth" | "disabled" | "error"
  externalAccountId?: string
  externalStoreId?: string
  scopes?: string[]
  tokenRef?: string
  metadata?: any
  lastSyncedAt?: number
  createdAt: number
  updatedAt: number
}
```

API tokens should generally be stored as provider secrets/environment variables or encrypted secret references, not plain table values.

## 7.21 `agentRuns`

```
agentRuns: {
  type:
    | "school_intake"
    | "brand_kit_generation"
    | "design_brief_generation"
    | "product_copy_generation"
    | "mockup_generation"
    | "shopify_publish"
    | "manus_storefront_update"
    | "ledger_reconciliation"
    | "payout_batch_generation"
    | "report_generation"
    | "ops_monitor"
  status: "queued" | "running" | "needs_human" | "completed" | "failed" | "cancelled"
  organizationId?: Id<"organizations">
  teamId?: Id<"teams">
  productId?: Id<"products">
  input: any
  output?: any
  logs?: string[]
  error?: string
  startedBy?: Id<"users">
  startedAt?: number
  completedAt?: number
  createdAt: number
  updatedAt: number
}
```

## 7.22 `approvalRequests`

```
approvalRequests: {
  organizationId: Id<"organizations">
  teamId?: Id<"teams">
  productId?: Id<"products">
  assetId?: Id<"assets">
  type: "brand_kit" | "design" | "product" | "storefront" | "payout"
  requestedBy: Id<"users">
  assignedToUserId?: Id<"users">
  assignedToRole?: string
  status: "pending" | "approved" | "rejected" | "expired"
  decisionNotes?: string
  decidedBy?: Id<"users">
  decidedAt?: number
  createdAt: number
  updatedAt: number
}
```

## 7.23 `auditLogs`

```
auditLogs: {
  actorUserId?: Id<"users">
  actorType: "user" | "agent" | "webhook" | "system" | "manus"
  action: string
  entityType: string
  entityId: string
  before?: any
  after?: any
  ipAddress?: string
  userAgent?: string
  createdAt: number
}
```

---

# 8. Auth and authorization

## 8.1 Auth provider

Use Clerk.

Reasons:

* fast onboarding
* organization support
* invitations
* MFA/social login options
* easier user management than rolling your own

## 8.2 Auth model

Use a hybrid authorization model:

```
Clerk authenticates identity.
Convex authorizes actions.
```

Clerk answers:

```
Who is this user?
```

Convex answers:

```
Can this user do this action on this school/team/product/payout?
```

## 8.3 Roles

Global roles:

```
"super_admin"
"operator"
"support"
"none"
```

Organization roles:

```
"school_finance_admin"
"athletic_director"
"coach"
"booster_admin"
"viewer"
```

## 8.4 Permission examples

```
canManageAll: super_admin
canCreateSchool: super_admin | operator
canApprovePayout: super_admin
canPreparePayout: super_admin | operator
canViewOrgFinance: super_admin | operator | school_finance_admin
canApproveDesign: super_admin | operator | athletic_director | coach
canPublishProduct: super_admin | operator
canOverrideRevenueShare: super_admin
canFreezePayout: super_admin
```

## 8.5 Required security rules

* School users can only access their own organization data.
* School users cannot view customer PII unless explicitly required.
* Buyer PII should stay in Shopify wherever possible.
* Payout math must be visible but not editable by school users.
* Every permission-sensitive action writes an audit log.
* Admin impersonation, if added, must be obvious and logged.

---

# 9. Backend API/function design

In Convex, most internal app operations should be Convex queries/mutations/actions. Public webhook endpoints should be Convex HTTP actions.

## 9.1 Admin queries

```
admin.getDashboardSummary()
admin.listOrganizations(filters)
admin.getOrganization(orgId)
admin.listTeams(orgId)
admin.getTeam(teamId)
admin.listStorefronts(filters)
admin.listProducts(filters)
admin.listApprovalQueue(filters)
admin.listPayoutBatches(filters)
admin.getPayoutBatch(batchId)
admin.getIntegrationHealth()
admin.getAuditLogs(filters)
```

## 9.2 Admin mutations

```
admin.createOrganization(input)
admin.updateOrganization(orgId, patch)
admin.createTeam(input)
admin.updateTeam(teamId, patch)
admin.generateTeamSlug(input)
admin.createRevenueShareRule(input)
admin.updateRevenueShareRule(ruleId, patch)
admin.inviteOrganizationMember(input)
admin.pauseTeam(teamId)
admin.unpauseTeam(teamId)
admin.archiveTeam(teamId)
admin.freezeOrganizationPayouts(orgId, reason)
admin.unfreezeOrganizationPayouts(orgId)
```

## 9.3 Product pipeline functions

```
products.createDraftProduct(input)
products.createStarterPack(teamId, templateIds)
products.attachDesignAsset(productId, assetId)
products.requestMockups(productId)
products.markMockupsReady(productId, assetIds)
products.verifyPrintfulSyncMapping(productId)
products.submitForAdminReview(productId)
products.approveAsAdmin(productId)
products.submitForSchoolReview(productId)
products.approveAsSchool(productId)
products.publishToShopify(productId)
products.pauseProduct(productId)
products.archiveProduct(productId)
```

## 9.4 Storefront functions

```
storefronts.createForTeam(teamId)
storefronts.updateSeo(storefrontId, input)
storefronts.publish(storefrontId)
storefronts.pause(storefrontId)
storefronts.syncFromShopify(storefrontId)
storefronts.requestManusBuild(storefrontId)
storefronts.requestManusUpdate(storefrontId, instructions)
```

## 9.5 Shopify integration actions

Use Shopify GraphQL Admin API where possible.

```
shopify.createCollection(teamId)
shopify.updateCollection(storefrontId, input)
shopify.createProduct(productId)
shopify.updateProduct(productId, patch)
shopify.assignProductToCollection(productId, collectionId)
shopify.publishProduct(productId)
shopify.unpublishProduct(productId)
shopify.syncOrder(shopifyOrderId)
shopify.syncOrdersSince(date)
shopify.syncRefund(shopifyRefundId)
```

## 9.6 Printful integration actions

```
printful.getCatalogProducts(filters)
printful.getCatalogVariant(variantId)
printful.createMockupTask(productId)
printful.retrieveMockupTask(taskId)
printful.createSyncProduct(productId)
printful.updateSyncProduct(productId)
printful.verifySyncVariantMappings(productId)
printful.syncFulfillmentStatus(orderId)
```

Printful mockup generation is asynchronous, so the system must store task IDs and retrieve results later or listen for completion webhooks.

## 9.7 Stripe integration actions

```
stripe.createConnectedAccount(orgId)
stripe.createOnboardingLink(orgId)
stripe.refreshConnectedAccountStatus(orgId)
stripe.createTransfer(payoutBatchId)
stripe.reverseTransfer(payoutBatchId, amountCents, reason)
stripe.createExpressDashboardLoginLink(orgId)
stripe.syncAccountRequirements(stripeAccountId)
```

Stripe Connect handles school payout rails after Convex calculates approved fundraiser earnings. It does not power the buyer checkout flow in the MVP.

## 9.8 Finance/ledger functions

```
finance.generateLedgerEntriesForOrder(orderId)
finance.generateLedgerEntriesForRefund(orderId, refundPayload)
finance.attachPrintfulCosts(orderId)
finance.recalculateOrderProfit(orderId)
finance.createMonthlyPayoutBatch(orgId, periodStart, periodEnd)
finance.createAllMonthlyPayoutBatches(periodStart, periodEnd)
finance.reviewPayoutBatch(batchId)
finance.approvePayoutBatch(batchId)
finance.sendPayoutBatch(batchId)
finance.generateMonthlyStatement(batchId)
finance.generateYearlySummary(orgId, year)
finance.exportLedgerCsv(filters)
```

## 9.9 Webhook HTTP endpoints

```
POST /webhooks/clerk
POST /webhooks/shopify/orders-create
POST /webhooks/shopify/orders-updated
POST /webhooks/shopify/refunds-create
POST /webhooks/shopify/fulfillments-updated
POST /webhooks/printful/mockup-task-finished
POST /webhooks/printful/order-updated
POST /webhooks/stripe
POST /webhooks/manus
```

Webhook requirements:

* verify signature/HMAC where provider supports it
* store raw payload in `webhookEvents`
* deduplicate by provider event ID
* process idempotently
* write audit logs
* do not trust webhook data alone for financial finality if reconciliation API fetch is available

Add table:

```
webhookEvents: {
  provider: "shopify" | "printful" | "stripe" | "clerk" | "manus"
  eventType: string
  providerEventId?: string
  payload: any
  status: "received" | "processed" | "failed" | "ignored"
  error?: string
  receivedAt: number
  processedAt?: number
}
```

---

# 10. File management

## 10.1 File types

ShopGo must handle:

* school logos
* mascot source images
* generated mascot designs
* print-ready files
* product mockups
* storefront hero images
* permission documents
* monthly statement PDFs
* CSV exports
* payout receipts

## 10.2 Storage recommendation

Use Convex storage for MVP. Move to S3/R2 later if needed for cost/performance.

Convex asset records should store:

* storage provider
* storage ID or external URL
* asset type
* owner type/ID
* approval state
* metadata
* checksum
* created by

## 10.3 File approval rules

* Raw uploaded school logo: `uploaded`
* AI-generated design: `needs_review`
* Print file: `approved` before product creation
* Mockup: `approved` before school review
* Permission doc: immutable once linked to permission record
* Financial reports: immutable once finalized

## 10.4 Image constraints

For print files:

* store original
* store processed print-ready file
* record dimensions/DPI/transparent background status
* record placement metadata
* record source prompt if AI-generated

## 10.5 Naming pattern

```
/{orgSlug}/{teamSlug}/brand/{timestamp}-{filename}
/{orgSlug}/{teamSlug}/designs/{productId}-{version}.png
/{orgSlug}/{teamSlug}/mockups/{productId}-{variant}.jpg
/{orgSlug}/reports/{year}/{month}-statement.pdf
```

---

# 11. Financial architecture

## 11.1 Core principle

The school should not need to understand Stripe to understand its fundraiser.

Stripe Express is useful but not sufficient as the school-facing finance experience. ShopGo needs its own finance dashboard and reports.

## 11.2 Revenue-share calculation

Default formula:

```
gross item sales
- item discounts
- item refunds
- Printful fulfillment cost
- payment/commerce fees if attributable
= net merch profit

school share = net merch profit × revenueSharePercent
platform share = net merch profit - school share
```

Do not include tax as fundraiser revenue.

Shipping should usually be excluded from commission unless shipping is intentionally marked up.

## 11.3 Payment and payout separation

Shopify checkout handles buyer payment.

Printful handles POD fulfillment.

Convex calculates fundraiser earnings.

Stripe Connect sends approved post-sale payouts to the school/booster account.

Because Shopify processes buyer payments, school payout funds do not automatically originate inside the Stripe Connect platform balance. ShopGo must treat Stripe Connect as a downstream disbursement system. Convex should calculate what each school is owed, create a payout batch, require admin approval, and then trigger a Stripe Connect transfer from ShopGo-controlled funds.

The scalable MVP pattern is:

```
Shopify checkout revenue
→ ShopGo reconciliation
→ Convex ledger
→ approved payout batch
→ Stripe Connect transfer
```

## 11.4 Payout cadence

Recommended MVP:

```
Monthly payouts
14-day reserve window
manual admin approval
Stripe transfer after approval
```

Example:

* June sales close June 30
* refunds/adjustments settle through July 14
* payout batch generated July 15
* admin approves
* Stripe transfer created

## 11.5 Payout reserve logic

Add configurable reserve:

```
reserveDays: 14
minimumPayoutCents: 2500
holdIfRefundRateAbovePercent: 10
holdIfAccountRestricted: true
holdIfPermissionExpired: true
```

## 11.6 Payout status logic

```
draft → pending_review → approved → processing → paid
                              ↘ failed
                              ↘ cancelled
```

## 11.7 School reporting

Each monthly statement should include:

* organization name
* team/storefront
* payout period
* gross sales
* discounts
* refunds
* fulfillment costs
* fees
* net merch profit
* school share
* payout date
* payout ID
* order count
* product sales summary
* adjustments
* disclaimer: not tax advice

---

# 12. Agent orchestration design

The agent system should be **human-in-the-loop**, not fully autonomous.

## 12.1 Agent design principle

Agents prepare work. Humans approve risk.

Automation can:

* draft
* classify
* generate
* check
* sync
* summarize
* recommend
* flag anomalies

Automation should not independently:

* approve school permissions
* publish unreviewed products
* send payouts
* override revenue-share rules
* use unapproved logos/mascots
* change legal/finance settings

## 12.2 Agent 1: School Intake Agent

Purpose:

Normalize school/team input.

Inputs:

```
schoolName
website
city
state
mascot
sport
colors
contacts
notes
```

Outputs:

```
normalizedOrganization
suggestedTeams
suggestedSlug
missingInfoChecklist
permissionRiskFlags
```

Actions:

* create draft org/team records
* suggest contacts
* generate onboarding checklist
* flag missing authorization

Human gate:

* admin confirms school/team identity

## 12.3 Agent 2: Permission Risk Agent

Purpose:

Prevent accidental IP/authorization issues.

Inputs:

```
schoolName
mascot
logoAsset
permissionRecords
storefrontText
```

Outputs:

```
riskLevel
missingPermissionTypes
recommendedNextAction
```

Human gate:

* admin must mark permissions as granted before publishing

## 12.4 Agent 3: Brand Kit Agent

Purpose:

Create structured brand system.

Inputs:

```
colors
logo
mascot
school website
style preference
```

Outputs:

```
brandKit
designRules
restrictedTerms
promptIngredients
```

Human gate:

* admin approves brand kit

## 12.5 Agent 4: Design Brief Agent

Purpose:

Generate product artwork briefs.

Inputs:

```
brandKit
sport
mascot
productTemplate
visualStyle
```

Outputs:

```
designPrompt
negativePrompt
placementNotes
printConstraints
```

Human gate:

* admin approves design brief before generation

## 12.6 Agent 5: Product Builder Agent

Purpose:

Turn approved design assets into product drafts.

Inputs:

```
team
productTemplates
approvedPrintFiles
pricingRules
```

Outputs:

```
productTitles
productDescriptions
variantSelection
pricingSuggestion
mockupRequests
```

Human gate:

* admin approves product drafts

## 12.7 Agent 6: Printful Mockup and Sync Agent

Purpose:

Create/retrieve Printful mockups and verify product/variant sync.

Inputs:

```
productId
printFileAssetIds
printfulTemplate
variantIds
placements
```

Outputs:

```
mockupTaskIds
mockupAssetIds
printfulSyncProductId
verifiedVariantMappings
```

Human gate:

* admin approves mockups before storefront use
* storefront cannot go live unless Shopify variant ↔ Printful variant mapping is verified

## 12.8 Agent 7: Shopify Publishing Agent

Purpose:

Create products/collections in Shopify.

Inputs:

```
productRecords
shopifyCollectionId
approvedCopy
approvedMockups
verifiedPrintfulMappings
```

Outputs:

```
shopifyProductIds
shopifyVariantIds
collectionAssignment
publishedUrls
```

Human gate:

* final publish approval

## 12.9 Agent 8: Manus Storefront Agent

Purpose:

Prepare a clean team storefront.

Inputs:

```
teamBrandKit
shopifyCollection
heroCopy
productGrid
campaignContext
```

Outputs:

```
manusPrompt
manusSessionId
storefrontStatus
recommendedChanges
```

Manus task examples:

```
Build a team storefront for Crestwood Ravens using the connected Shopify collection "Crestwood Ravens." Use a bold varsity athletic look, feature hoodies first, include a fundraiser message, and route checkout through the Shopify checkout experience.
```

```
Audit the Crestwood Ravens collection. Fix missing alt text, product descriptions, and collection copy. Do not change prices.
```

Human gate:

* admin reviews Manus changes before launch where possible

## 12.10 Agent 9: Reconciliation Agent

Purpose:

Compare Shopify, Printful, Stripe, and Convex.

Inputs:

```
orders
refunds
fulfillmentCosts
ledgerEntries
payoutBatches
stripeTransfers
```

Outputs:

```
reconciliationStatus
anomalies
missingCosts
duplicateOrders
refundAdjustments
recommendedManualReview
```

Human gate:

* admin approves payout batch

## 12.11 Agent 10: Finance Report Agent

Purpose:

Generate school-friendly reports.

Inputs:

```
ledgerEntries
payoutBatch
organization
team
```

Outputs:

```
monthlyStatementPdf
csvExport
yearlySummary
plainEnglishNotes
```

Human gate:

* admin finalizes reports

## 12.12 Agent 11: Ops Monitor Agent

Purpose:

Catch broken workflows.

Checks:

* failed Shopify sync
* missing Printful cost
* missing Printful sync mapping
* mockup task stuck
* Stripe account restricted
* payout batch waiting too long
* team storefront unpublished
* school permission expired
* product has no collection
* Shopify product exists but no Convex mapping
* Shopify variant exists but no Printful mapping
* order has no team attribution

Runs:

```
hourly for integration health
daily for ops summary
monthly for payout readiness
```

---

# 13. Admin dashboard specification

## 13.1 Global dashboard

Widgets:

* active teams
* pending launches
* pending approvals
* failed integrations
* monthly gross sales
* estimated school payouts
* payout batches awaiting approval
* top teams by sales
* recent orders
* agent failures

## 13.2 Schools page

Columns:

* school/org name
* status
* location
* teams count
* connected account status
* unpaid balance
* last payout
* permission status
* actions

## 13.3 Team detail page

Sections:

* team profile
* storefront URL
* brand kit
* products
* approvals
* orders
* fundraiser summary
* permissions
* Manus tasks
* audit log

Admin actions:

* edit profile
* regenerate slug
* create starter pack
* request Manus update
* pause storefront
* unpublish products
* create payout hold

## 13.4 Product pipeline page

Kanban columns:

```
Draft
Designing
Mockup Requested
Mockup Ready
Admin Review
School Review
Approved
Published
Error
```

## 13.5 Finance command center

Views:

* unpaid balances by school
* monthly payout batches
* payout holds
* failed Stripe transfers
* refund adjustments
* reconciliation anomalies
* report generation status

## 13.6 Integration health

Show:

* Shopify checkout/order status
* Shopify product/collection sync status
* Printful product sync status
* Printful variant mapping status
* Printful fulfillment status
* Convex ledger status
* Stripe Connect account status
* payout batch status
* school report status
* Manus sync status
* last successful webhook
* failed webhook count
* stuck jobs
* token/scopes issues

Avoid presenting Stripe as the payment source for buyer orders. Present Stripe as the school payout rail.

## 13.7 Audit log

Filter by:

* user
* agent
* school
* team
* product
* payout
* action
* date range

---

# 14. School dashboard specification

## 14.1 School overview

School finance/admin users see:

* active team stores
* gross sales
* estimated fundraiser earnings
* pending payout
* last payout
* reports
* Stripe onboarding status

## 14.2 Team storefront card

Shows:

* team name
* public URL
* store status
* sales this month
* best sellers
* pending approvals

## 14.3 Approvals

School can approve/reject:

* brand kit
* mascot design
* product mockups
* storefront preview

Decision record includes:

* user
* timestamp
* notes
* version approved

## 14.4 Finance reports

Download:

* monthly statement PDF
* order summary CSV
* yearly fundraiser summary
* payout receipts

## 14.5 Stripe account area

School can:

* start onboarding
* resume onboarding
* open Stripe Express dashboard/login link
* see payout account status
* see whether payouts are enabled

They should not need to use Stripe to understand fundraiser performance.

## 14.6 School-facing explanation language

School finance admins should see the model explained like this:

```
Supporters purchase through a Shopify checkout experience.
Printful powers print-on-demand fulfillment, packing, and shipping.
ShopGo calculates fundraiser earnings.
Stripe Connect sends approved post-sale payouts.
```

---

# 15. Shopify implementation details

## 15.1 One central Shopify store

Recommended MVP:

```
One Shopify store: ShopGo.team
Many team collections/pages
```

Reasons:

* simpler operations
* easier product management
* lower app complexity
* centralized checkout
* centralized reporting
* consistent buyer experience

## 15.2 Collection strategy

Each team gets one Shopify collection.

Example:

```
Collection title: Crestwood Ravens
Handle: crestwood-ravens
```

Use either:

* manual/custom collection with explicit products, or
* smart collection using product tag like `team:crestwood-ravens`

Recommendation:

Use smart collections for scale if product tags are reliable. Use manual collections if you want maximum control.

## 15.3 Product tags/metafields

Every Shopify product should include:

```
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

Use Shopify metafields for stable IDs, not just tags.

## 15.4 Required Shopify scopes

Likely scopes:

```
read_products
write_products
read_orders
write_orders
read_all_orders
read_inventory
read_fulfillments
read_returns
write_discounts
```

Need careful app setup because Shopify order access may have access-window/scoping limitations depending on app permissions and review status.

---

# 16. Printful implementation details

## 16.1 Starter pack

MVP starter pack:

* short-sleeve tee
* hoodie
* crewneck
* long sleeve
* hat
* women’s tee or youth tee if audience demands
* optional sweatpants
* optional sticker

## 16.2 Product template mapping

Every ShopGo `productTemplate` maps to:

```
Printful catalog product ID
allowed variant IDs
placements
colors
sizes
base cost estimate
```

## 16.3 Product sync and Shopify publishing

The correct product pipeline is:

```
Choose Printful blank product
→ create design/print file
→ create Printful sync product
→ push/sync to Shopify product + variants
→ assign product to team collection
→ verify Shopify variant ↔ Printful variant mapping
→ publish to team storefront
```

This gives the cleanest buyer experience and the least fulfillment risk.

## 16.4 Mockup generation

Flow:

```
create mockup task → store task ID → poll/retrieve result or receive webhook → save mockup assets → update product status
```

Printful mockup generation is asynchronous, so the system must store task IDs and retrieve results later or listen for completion webhooks.

## 16.5 Fulfillment cost sync

At order reconciliation time, fetch actual Printful costs if possible.

Do not rely permanently on estimated product costs for final payout.

## 16.6 Required go-live rule

Before a product can be published:

* product must be attached to a team
* product must be assigned to a Shopify collection
* Shopify product ID must exist
* Shopify variant IDs must exist
* Printful sync product ID must exist
* Printful variant mappings must be verified
* mockups must be approved
* print files must be approved
* pricing must be approved
* revenue-share rule must be attached
* permissions must be valid
* storefront must be active or ready for publishing

---

# 17. Stripe Connect implementation details

## 17.1 Recommended account approach

Use Stripe Connect Express for MVP, but abstract the data model so you can later use newer account/controller-based configurations if Stripe recommends that for your platform.

## 17.2 Connected account timing

Do not require school onboarding before a store launches.

Better:

```
Store can launch after written permission.
Stripe onboarding required before first payout.
```

This lowers sales friction.

## 17.3 Transfer timing

Only transfer after:

* payout batch approved
* reserve window passed
* connected account payouts enabled
* no permission/legal hold
* no reconciliation anomaly
* minimum payout met

## 17.4 Stripe is not the school report

Stripe is a banking/tax rails layer. ShopGo is the fundraiser record layer.

This matters because schools need reports by team/campaign, not just deposit history.

## 17.5 Stripe is downstream, not checkout

Stripe Connect does not process buyer payments in the MVP.

Correct model:

```
Buyer pays through Shopify checkout.
Convex calculates school payout obligation.
Stripe Connect sends approved post-sale payout.
```

---

# 18. Manus implementation details

## 18.1 Manus task types

Store all Manus tasks in:

```
manusTasks: {
  teamId?: Id<"teams">
  storefrontId?: Id<"teamStorefronts">
  taskType:
    | "build_storefront"
    | "update_collection"
    | "audit_products"
    | "create_campaign"
    | "update_copy"
    | "analyze_sales"
  prompt: string
  status: "draft" | "sent" | "running" | "completed" | "failed" | "manually_verified"
  manusSessionId?: string
  resultSummary?: string
  createdBy: Id<"users">
  createdAt: number
  updatedAt: number
}
```

## 18.2 Manus safety rule

Any Manus action that changes Shopify should be mirrored back to Convex through sync.

After Manus updates products/collections:

```
Run Shopify sync → compare expected vs actual → write audit log → show diff
```

## 18.3 Recommended Manus use

Good:

```
Create a storefront for this collection.
Rewrite product descriptions.
Audit missing alt text.
Create campaign copy.
Reorganize products into collections.
```

Avoid:

```
Calculate payouts.
Change school revenue percentages.
Decide whether a school has authorization.
Send money.
```

---

# 19. Data privacy

## 19.1 Buyer PII

Keep buyer PII in Shopify unless operationally necessary.

In Convex:

* store hashed email for repeat/order matching
* store order totals
* store line items
* store fulfillment status
* avoid shipping addresses unless necessary

## 19.2 School financial data

Protect:

* payout records
* connected account status
* reports
* permission documents
* bank/tax info links

Never store raw bank info. Let Stripe handle that.

## 19.3 Manus data exposure

Because Manus can read order-level data through Shopify for campaign/sales analysis, restrict OAuth scopes and operational use to what is needed.

---

# 20. MVP build phases

## Phase 1 — Admin operating system

Build:

* Clerk auth
* super admin dashboard
* organizations
* teams
* team slug system
* brand kits
* permissions
* product templates
* assets
* audit logs

No payout automation yet.

## Phase 2 — Shopify/Printful product pipeline

Build:

* Shopify collection creation
* product draft creation
* Printful template mapping
* Printful sync product creation
* Shopify product/variant sync
* Printful variant mapping verification
* mockup task tracking
* product approval states
* publish/unpublish products
* storefront mapping

## Phase 3 — Orders and ledger

Build:

* Shopify order webhooks
* order/item storage
* refund storage
* Printful fulfillment/cost sync
* ledger entry generation
* team attribution
* monthly revenue summaries

## Phase 4 — Stripe Connect payouts

Build:

* connected account creation
* onboarding link
* account status sync
* payout batch generation
* admin approval
* Stripe transfer
* payout receipts

## Phase 5 — School dashboard

Build:

* school login
* team overview
* approvals
* sales reports
* payout history
* monthly/yearly exports

## Phase 6 — Manus orchestration

Build:

* Manus task records
* prompt templates
* storefront update queue
* Shopify post-Manus diff checker
* admin review workflow

## Phase 7 — Agent automation

Build:

* intake agent
* brand agent
* design brief agent
* product builder
* Printful mockup/sync agent
* reconciliation agent
* ops monitor
* report generator

---

# 21. Non-negotiable engineering principles

1. **Ledger-first finance.**  
  Never calculate school payouts casually from a live orders query.
2. **Human approval for risk.**  
  Designs, permissions, products, and payouts need approval gates.
3. **Convex owns state.**  
  Shopify/Printful/Stripe/Manus are integrations, not your product brain.
4. **Immutable money records.**  
  Corrections create new entries.
5. **Team storefront is the core object.**  
  Everything hangs from `teamStorefrontId`.
6. **Shopify checkout stays intact.**  
  Do not overcomplicate checkout before validating demand.
7. **Printful sync must be verified before publish.**  
  A Shopify product is not publishable until its Printful product/variant mapping is confirmed.
8. **Schools get reports, not just deposits.**  
  Stripe Express is not the school finance dashboard.
9. **Storefront generation should be replaceable.**  
  Manus is powerful, but ShopGo should still survive without Manus serving the page.

---

# 22. Recommended Codex build prompt direction

Use this as the build instruction:

```
Build ShopGo.team as a Next.js + Convex + Clerk application for managing high school team merch storefronts.

The core entity is teamStorefront. Each teamStorefront maps one school/team to one public slug, one Shopify collection, one product pipeline, one approval workflow, one fundraiser ledger, and one payout/reporting workflow.

Buyers purchase through a Shopify checkout experience. Printful powers product sync, POD fulfillment, packing, and shipping. Stripe Connect powers post-sale school payouts after Convex calculates fundraiser earnings.

Convex is the operating system and source of truth for ShopGo workflow state, approvals, financial ledgering, payout calculations, reports, audit logs, and integration mappings.

Shopify is the commerce source of truth for checkout, order records, customer purchase flow, product listings, and collections.

Printful is the fulfillment source of truth for POD products, print files, variant mappings, mockups, fulfillment, packing, and shipping.

Stripe Connect is the payout rail for schools and booster organizations after ShopGo calculates and approves payout amounts.

Manus is an assisted Shopify/storefront operator. It may help create storefront pages, update collection copy, organize products, create campaign materials, and assist with Shopify operations, but it must not be treated as the source of truth for financial records, school permissions, payout logic, or approval state.

Start by implementing the admin dashboard, Convex schema, Clerk auth, team/storefront/product data model, file metadata, approval workflow, audit logs, and placeholder integrations. Do not implement payout transfers until the ledger, reconciliation, reserve window, and admin approval workflow exist.
```

---

# 23. Biggest remaining architectural decisions

Before you start building, I would still decide these:

1. **Will the public storefronts be primarily Manus-served or Next.js-served?**  
  My recommendation: build Next.js fallback no matter what.
2. **Will each team get one collection or one collection per sport/category?**  
  My recommendation: one team collection first, optional nested tags later.
3. **Will school share be percentage of net profit or fixed amount per item?**  
  My recommendation: percentage of net merch profit for flexibility, but fixed-per-item is easier to explain.
4. **Will the payout recipient usually be the school, booster club, or athletic association?**  
  This affects onboarding, tax reporting, and permission docs.
5. **Will you target U.S. schools first?**  
  This affects Stripe, tax forms, reporting language, and fundraising expectations.

---

# Final recommendation

Build ShopGo as a **fundraising operations platform**, not merely a merch storefront tool.

The winning product is not “AI makes school merch.”

The winning product is:

> **A school-safe, finance-friendly, admin-controlled system that launches team merch stores fast, tracks every sale clearly, and pays schools with clean fundraiser reporting.**

That is the moat.

Convex gives you the operating brain. Shopify gives you the checkout and commerce reliability. Printful gives you product sync and POD fulfillment. Stripe Connect gives you payout infrastructure. Manus gives you speed. Your ledger, approvals, and reports make the whole thing trustworthy.
