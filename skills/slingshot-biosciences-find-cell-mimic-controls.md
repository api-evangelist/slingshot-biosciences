---
name: Find Slingshot Bio cell mimic controls
description: Search the Slingshot Bio catalog for a flow cytometry control and read back its variants, price and availability, using either the MCP server or the Storefront GraphQL API.
api: graphql/slingshot-biosciences-storefront.graphql
surfaces:
  mcp: https://slingshot-bio.myshopify.com/api/mcp
  graphql: https://slingshot-bio.myshopify.com/api/2026-07/graphql.json
operations: [search_catalog, get_product_details, search, products, product, productByHandle, collection, collections]
generated: '2026-08-05'
method: generated
---

# Find Slingshot Bio cell mimic controls

Slingshot Bio sells synthetic cell mimics used as reference materials in flow
cytometry. The catalog is organised by what the control is *for* — compensation
and unmixing, viability, immunophenotyping, quantitation, potency, scatter and
instrument calibration — under brand names such as **ViaComp**, **SpectraComp**,
**TruCytes**, **FlowCytes**, **StimCytes**, **QuantCytes** and **ScatterBridge**.

Both routes below are anonymous. No key, no token, no sign-up.

## Route A — MCP (preferred for agents)

`POST https://slingshot-bio.myshopify.com/api/mcp` with JSON-RPC 2.0.

1. `tools/list` to confirm the five tools are present.
2. **`search_catalog`** — pass a natural-language query, filters, or both. At
   least one of the two is required. Results are paginated and the first page is
   deliberately small.
3. **`get_product_details`** — pass `product_id` (a `gid://shopify/Product/...`
   global id taken from the search result). Pass `options` to pin a specific
   variant; omit it and the first available variant is returned. `country` and
   `language` are optional.
4. **`search_shop_policies_and_faqs`** — pass `query` for shipping, returns,
   hours or contact questions. Do not guess these from the product page.

## Route B — Storefront GraphQL

`POST https://slingshot-bio.myshopify.com/api/2026-07/graphql.json`,
`Content-Type: application/json`.

- `search(query:)` or `products(first:, query:)` for keyword search;
  `predictiveSearch` for typeahead.
- `product(id:)` or `productByHandle(handle:)` for one item. Handles are
  lowercase slugs — `viacomp`, `spectracomp`, `tbnk-mimic`.
- `collections(first:)` / `collection(handle:)` to browse by purpose. Collections
  are the honest way to answer "which control do I need for spectral unmixing" —
  free-text search is not.
- Price and stock live on `ProductVariant`: `price { amount currencyCode }`,
  `availableForSale`, `quantityAvailable`.

## Rules

- **Paginate with Relay cursors.** `first`/`after` plus
  `pageInfo { hasNextPage endCursor }`. Never assume one page is the whole catalog.
- **Watch your query cost.** The response carries
  `extensions.cost.requestedQueryCost`. There are no rate-limit headers — cost is
  the only signal you get.
- **Errors arrive with HTTP 200.** Inspect `errors[]` and `errors[].extensions.code`
  before trusting `data`. See `errors/slingshot-biosciences-problem-types.yml`.
- **Pin the API version.** `2026-07` is current; `2025-10`, `2026-01` and `2026-04`
  are still supported. Query `publicApiVersions` rather than assuming.
- Product names carry ® and ™ marks (`ViaComp®`, `TBNK Mimic™`). Match on `handle`,
  not on the display title.
