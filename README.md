# Slingshot Biosciences

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Slingshot Biosciences is an Emeryville, California biotechnology company that engineers synthetic
cell mimics — hydrogel-based particles that behave like real cells — as reproducible reference
materials and controls for flow cytometry, spectral unmixing, immunophenotyping, cell and gene
therapy potency assays, and instrument standardization. Product families include TruCytes,
ViaComp, FlowCytes, ScatterBridge, SpectraComp, QuantCytes and StimCytes.

For a life-sciences vendor it publishes an unusually complete agent-facing surface:

- **`/llms.txt`** — orientation document naming the sitemaps, collections, blog and case studies.
- **`/.well-known/api-catalog`** — an RFC 9727 linkset declaring two machine-readable APIs
  (Shopify Storefront GraphQL and a Sanity Content Lake GROQ endpoint). The `api-catalog` link
  relation is also served in the `Link` header of every page.
- **Markdown twins** — every canonical URL returns `text/markdown` to a client sending
  `Accept: text/markdown`, with `Vary: Accept` and `X-Robots-Tag: noindex`. A markdown content
  index is published at `/sitemap.md`.
- **A live MCP server** — `https://slingshot-bio.myshopify.com/api/mcp` answers anonymous
  `tools/list` with five tools; customer-scoped operations are protected by an OAuth 2.0
  authorization server advertised at `/.well-known/oauth-protected-resource`.

There is no OpenAPI. The machine-readable contract is the anonymously introspectable Storefront
GraphQL schema (428 types), captured in `graphql/`.

- https://www.slingshotbio.com/
- https://www.hiive.com/securities/slingshot-biosciences-stock
