# Endeavour Energy (endeavour-energy)

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

Endeavour Energy is the regulated electricity distribution network service provider (DNSP) for Greater Western Sydney, the Blue Mountains, the Illawarra, the Southern Highlands, the South Coast and Central West of New South Wales, Australia, delivering power to around 2.8 million people over the poles-and-wires network it owns and operates. It sits between transmission and retail in the value chain - it moves electricity and hosts the meters, but it does not sell energy to consumers, so it holds no retail billing relationship. Its API posture is a sharp split. On the market and network side it is genuinely open: a live Opendatasoft portal at data.endeavourenergy.com.au serves eight network datasets (poles, conductors, distribution districts, other network assets, distribution substation available capacity, and live planned/unplanned/single-premise outages) through a documented, anonymous, no-key REST Explore API with a downloadable OpenAPI 3.0.3 contract, most of it under the Open Database Licence. On the consumer side it is closed and, more importantly, unobligated: Australia's Consumer Data Right was extended to energy, but the designation put the data-holder obligation on electricity retailers as primary holders and AEMO as the secondary holder and gateway, not on distributors. Endeavour Energy appears nowhere in the 84 brands of the CDR energy data holder register, publishes no CDR base URI, and makes no reference to the Consumer Data Right anywhere on its corporate site. It does operate a CSIP-AUS (Common Smart Inverter Profile - Australia) utility server for NSW flexible exports and the emergency backstop mechanism, but that machine interface is device-facing and entirely undocumented publicly. The open data portal itself is not linked from the corporate website.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/endeavour-energy/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/endeavour-energy/refs/heads/main/apis.yml)

## Tags

- Energy
- Australia
- Utilities
- Electricity
- Grid
- Open Data
- Energy Networks
- Distribution
- Outages
- Consumer Data Right
- Smart Metering
- DER
- Solar
- Renewables

## Timestamps

- **Created:** 2026-07-27
- **Modified:** 2026-07-27

## APIs

### Endeavour Energy Open Data Explore API

Anonymous, key-free REST API over Endeavour Energy's public network open data catalogue, served on the Opendatasoft Explore API v2.1. Exposes eight datasets covering distribution network assets (440,728 poles, 808,500 conductor segments, 316,997 other network assets, distribution districts), 30,500 records of distribution substation available capacity, and live outage feeds (unplanned, planned and single-premise) that refresh roughly every ten minutes. Supports dataset search, record query via the Opendatasoft Query Language (ODSQL), faceting, per-record lookup, attachments, and bulk export to CSV, Parquet, GPX and DCAT.

- **Human URL:** [https://data.endeavourenergy.com.au/pages/home/](https://data.endeavourenergy.com.au/pages/home/)
- **Base URL:** `https://data.endeavourenergy.com.au/api/explore/v2.1`

#### Tags

- Open Data
- Energy
- Electricity
- Grid
- Outages
- Network Assets
- Australia
- Geospatial

#### Properties

- [OpenAPI](openapi/endeavour-energy-open-data-explore-api-v2-1-openapi.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [OpenAPI](openapi/endeavour-energy-open-data-explore-api-v2-0-openapi.json) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html) (deprecated v2.0)
- [Documentation](https://data.endeavourenergy.com.au/pages/home/)
- [API Reference](https://data.endeavourenergy.com.au/api-console/explore/v2.1/)
- [Portal](https://data.endeavourenergy.com.au/explore/)

## Mandate and access posture

| Axis | Finding |
| --- | --- |
| Mandate regime | `none` — CDR energy considered and ruled out |
| Mandate status | `not-applicable` — never designated, not merely un-built |
| Data standard | Opendatasoft Explore API v2.1 (open data); CSIP-AUS / IEEE 2030.5 for DER, undocumented |
| Consumer data API | No |
| Market data open | Yes |
| Access gate | `self-serve` — no signup, no key, 5,000 anonymous calls/day |
| Auth model | Optional `apikey` query parameter; anonymous access works on every endpoint tested |
| Home market | Australia |

Australia's Consumer Data Right — the same statutory regime, regulator and Data Standards Body that produced the byte-for-byte identical fifty-bank banking contract — was extended to energy. It did not reach the distribution layer. The designation makes electricity **retailers** the primary data holders and **AEMO** the secondary data holder and gateway. Querying the authoritative CDR register on 2026-07-27 returned 84 energy data holder brands, all retailers, with zero matches for Endeavour Energy or for any other Australian distribution network business.

The result is the inverse of the banking pattern: the organization that physically owns the meters on 2.8 million people's homes has no consumer data obligation at all, while publishing its entire grid — every pole, every conductor, every substation's spare capacity, and live outages — through an open API nobody compelled it to build.

See [review.yml](review.yml) for every URL probed, the HTTP status of each, and the full mandate, standard, split, gate and auth findings.
