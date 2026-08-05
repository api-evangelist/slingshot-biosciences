---
name: Read slingshotbio.com as markdown
description: Ingest Slingshot Bio's product, application and resource content as clean markdown using HTTP content negotiation, the markdown sitemap, llms.txt and the RFC 9727 api-catalog — instead of scraping rendered HTML.
api: null
surfaces:
  website: https://www.slingshotbio.com
  content_lake: https://9l0m1rv5.api.sanity.io/v2025-08-29/data/query/production
operations: [GET / with Accept text/markdown, GET /sitemap.md, GET /llms.txt, GET /.well-known/api-catalog]
generated: '2026-08-05'
method: generated
---

# Read slingshotbio.com as markdown

Do not scrape this site's HTML. It serves a markdown twin of every canonical URL
to any client that asks correctly, and the twin is stripped of styling, tracking
pixels and JavaScript.

## The three entry points

1. **`GET https://www.slingshotbio.com/llms.txt`** — the orientation document.
   Names the sitemaps, the product collections, the blog and the case studies,
   and states the content-negotiation contract explicitly.
2. **`GET https://www.slingshotbio.com/sitemap.md`** — the full content index in
   markdown. ~184 URLs: products, brands, applications, industries, resources,
   blog posts, press releases, webinars, events, posters, technical and safety
   data sheets. Use this as the crawl frontier, not a link crawl.
3. **`GET https://www.slingshotbio.com/.well-known/api-catalog`** — an RFC 9727
   linkset naming the two machine-readable APIs behind the site. The
   `api-catalog` link relation is also present in the `Link` header of every
   page, so you can discover it from any response.

## The negotiation

Send `Accept: text/markdown` on any canonical URL.

```
GET /products/viacomp HTTP/1.1
Host: www.slingshotbio.com
Accept: text/markdown
```

The response is `Content-Type: text/markdown; charset=utf-8` with
`Vary: Accept` and `X-Robots-Tag: noindex`. The identical URL without the header
returns `text/html`. Markdown responses open with an H1 title, a blockquote
summary, and `**Published:**` / `**Updated:**` dates — take dates from there
rather than parsing prose.

## What lives where

- **Products, variants, price, stock** — Storefront GraphQL, not the markdown.
  See the *Find cell mimic controls* skill.
- **Applications, industries, brands, how-it-works, scientific publications,
  press releases, webinars, events, data sheets** — markdown twins.
- **Structured editorial documents** — the Sanity Content Lake GROQ endpoint in
  the api-catalog answers anonymous queries if you need the underlying documents
  rather than the rendered page.

## Rules

- **Respect robots.txt.** `/api/`, `/search`, `/cart`, `/saved`, `/og-preview`
  and `/mega-menu-test` are disallowed for all user agents. There are no
  AI-specific directives — agents get the same rules as everyone else.
- **Do not index the markdown.** It is explicitly marked `noindex` as a duplicate
  of the HTML. It is for reading, not for republishing.
- **Prefer the sitemap over crawling.** `/sitemap.xml` is canonical and covers
  every locale; `/sitemap.md` is the readable equivalent.
- Product marketing pages are not a substitute for the technical and safety data
  sheets under `/resources/technical-data-sheet` and
  `/resources/safety-data-sheet`. For assay-relevant claims, cite those.
