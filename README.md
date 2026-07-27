# Endeavour Energy (endeavour-energy)

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
