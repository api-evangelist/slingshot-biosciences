---
name: Build a Slingshot Bio cart and hand off to checkout
description: Create a cart, add cell mimic variants, apply buyer and delivery details, and hand the buyer a checkout URL — via the MCP update_cart tool or the equivalent Storefront GraphQL cart mutations.
api: graphql/slingshot-biosciences-storefront.graphql
surfaces:
  mcp: https://slingshot-bio.myshopify.com/api/mcp
  graphql: https://slingshot-bio.myshopify.com/api/2026-07/graphql.json
operations: [update_cart, get_cart, cartCreate, cartLinesAdd, cartLinesUpdate, cartLinesRemove, cartBuyerIdentityUpdate, cartDeliveryAddressesAdd, cartSelectedDeliveryOptionsUpdate, cartDiscountCodesUpdate, cartNoteUpdate, cart]
generated: '2026-08-05'
method: generated
---

# Build a Slingshot Bio cart and hand off to checkout

The agent's job ends at the checkout URL. Payment is not exposed to the MCP
surface — `cartSubmitForCompletion` and `cartPaymentUpdate` exist only in
GraphQL, and an agent should hand the human the `checkoutUrl` rather than
attempt completion.

## Route A — MCP, one consolidated call

`update_cart` is the whole flow. It is the one place the MCP surface is *more*
capable than GraphQL: twelve separate GraphQL mutations collapse into one tool.

1. **Create.** Call `update_cart` with `add_items` only and no `cart_id`. This
   performs a `cartCreate`. Keep the returned cart id.
2. **Amend.** Call `update_cart` again with `cart_id` plus any of
   `add_items`, `update_items`, `remove_line_ids`, `buyer_identity`,
   `delivery_addresses_to_add`, `delivery_addresses_to_replace`,
   `selected_delivery_options`, `discount_codes`, `gift_card_codes`, `note`.
3. **Read back.** `get_cart` with `cart_id` returns lines, shipping options,
   discount info and the checkout URL.

**Shipping options only appear after both items and a delivery address are on the
cart.** Do not report "no shipping available" before you have added an address.

## Route B — Storefront GraphQL

Same sequence, one mutation at a time:

`cartCreate` → `cartLinesAdd` / `cartLinesUpdate` / `cartLinesRemove` →
`cartBuyerIdentityUpdate` → `cartDeliveryAddressesAdd` →
`cartSelectedDeliveryOptionsUpdate` → `cartDiscountCodesUpdate` →
`cartNoteUpdate`, then `cart(id:)` for `checkoutUrl`.

Cart lines reference `ProductVariant` merchandise ids
(`gid://shopify/ProductVariant/...`), **not** product ids. Resolve the variant
first — see the *Find cell mimic controls* skill.

## Rules

- **There is no idempotency key.** Nothing on this surface accepts one. Calling
  `cartLinesAdd` or `update_cart` twice with the same items adds the line twice.
  Track your own request state; on a timeout, `get_cart` and reconcile before
  retrying. This is the single most important operational fact about this API.
- **Check `userErrors`.** Every cart mutation returns a typed `userErrors`
  payload (`CartUserError`) alongside the cart. A mutation can return HTTP 200,
  no `errors[]`, and still have failed.
- **Ordering matters.** Buyer identity and delivery address before delivery
  options; delivery options before you quote a total.
- **Customer accounts need OAuth.** Attaching an authenticated customer requires
  a token from `https://shopify.com/authentication/60285812930` with the
  `customer-account-api:full` scope. See `scopes/slingshot-biosciences-scopes.yml`.
- **Many Slingshot products are quote-driven.** Custom biomarker mimics and bulk
  orders route through `https://www.slingshotbio.com/request-a-quote-for-cell-mimics`,
  not the cart. If a search returns no purchasable variant, send the buyer there
  rather than reporting the product unavailable.
