---
generated: '2026-07-27'
method: generated
name: Discover what Endeavour Energy publishes
description: Enumerate the open data catalogue, read a dataset's field schema, and learn its licence before you build anything on it.
api: openapi/endeavour-energy-open-data-explore-api-v2-1-openapi.json
operations: [getDatasets, getDataset, getDatasetsFacets, getRecordsFacets, getDatasetAttachments]
source: >-
  operationIds verified verbatim in
  openapi/endeavour-energy-open-data-explore-api-v2-1-openapi.json; behaviour
  verified with live anonymous requests on 2026-07-27.
---

# Discover what Endeavour Energy publishes

The first thing to do against this API, and the step most consumers skip. The OpenAPI contract types every row as an untyped `fields` object, so until you have read a dataset's schema you cannot write a valid query — and until you have read its licence you cannot legally redistribute what you get.

## Auth

None. Do not send a credential. `GET https://data.endeavourenergy.com.au/api/explore/v2.1/catalog/datasets` returns 200 anonymously. See `authentication/endeavour-energy-authentication.yml`.

## Steps

1. **List the catalogue** — `getDatasets` (`GET /catalog/datasets`). Expect `total_count: 8`. For each result read `dataset_id` and `metas.default`: `title`, `records_count`, `license`, `modified`, `theme`.
2. **Check the licence before you go further.** Five datasets declare the Open Database Licence. Three declare `license: null` — `conductors_hv_lv_sl_ug`, `distribution-substation-available-capacity` and `distribution-district`. That is 839,001 records with no stated reuse terms. Treat undeclared as "ask before republishing", not as public domain.
3. **Read the field schema** — `getDataset` (`GET /catalog/datasets/{dataset_id}`). The `fields[]` array gives each column's `name`, `label` and `type`. This is the only place the domain model is described. Watch for date-like columns typed `text` rather than `date` — on `outagecustomerlive`, `start_date_time`, `end_date_time`, `est_restore_time` and `updated_at` are all `text`.
4. **Understand the shape of the data** — `getRecordsFacets` (`GET /catalog/datasets/{dataset_id}/facets`) returns facet values with counts, which is the cheapest way to see the distinct values in a column without paginating records. Use `getDatasetsFacets` (`GET /catalog/facets`) for catalogue-level facets such as theme and publisher.
5. **Check for attachments** — `getDatasetAttachments` (`GET /catalog/datasets/{dataset_id}/attachments`). Some datasets ship supporting documents.

## Errors

- `404` with `{"error_code": "NotFoundResource"}` if the `dataset_id` is wrong. This status is **not** declared in the OpenAPI contract — handle it anyway. See `errors/endeavour-energy-problem-types.yml`.
- `400` with `{"error_code": "ODSQLSyntaxError"}` if an ODSQL clause is malformed; the message names the offending token and position.

## Rate limits

5,000 calls/day, shared across every anonymous consumer of the domain. Read `X-RateLimit-Remaining` on each response. Discovery is cheap — six calls covers the whole catalogue — so do it once and cache the schema rather than re-reading it per query. See `rate-limits/endeavour-energy-rate-limits.yml`.

## Notes

- Every operation is a GET and is safe to retry.
- The only fresh datasets are the three outage feeds. `endeavourenergy_poles` was last modified 2026-03-17 and `distribution-district` 2025-07-23; check `metas.default.modified` before assuming currency.
