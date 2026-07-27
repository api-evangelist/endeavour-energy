---
generated: '2026-07-27'
method: generated
name: Track live power outages
description: Poll Endeavour Energy's three live outage feeds for unplanned, planned and single-premise supply interruptions, with location, cause, customers affected and estimated restore time.
api: openapi/endeavour-energy-open-data-explore-api-v2-1-openapi.json
operations: [getDataset, getRecords, getRecord, getRecordsFacets]
source: >-
  operationIds verified verbatim in
  openapi/endeavour-energy-open-data-explore-api-v2-1-openapi.json; field names
  read from the live dataset schema of outagecustomerlive on 2026-07-27.
---

# Track live power outages

The most time-sensitive thing this API publishes. Three datasets carry current supply interruptions across Endeavour Energy's network, refreshing roughly every ten minutes, with coordinates, incident identifiers, cause, customers affected and an estimated restore time. No credential is required.

## Auth

None. Anonymous GET.

## The three feeds

| dataset_id | covers |
|---|---|
| `outagecustomerlive` | Live unplanned outages |
| `plannedoutagecustomer` | Scheduled/planned outages |
| `single_premise_outage_customer_live` | Live outages affecting one premise |

## Steps

1. **Confirm the schema once** — `getDataset` (`GET /catalog/datasets/outagecustomerlive`). Current fields: `incident_id`, `incident_status`, `outage_type`, `outage_cause_main`, `outage_cause_sub`, `cause`, `start_date_time`, `end_date_time`, `est_restore_time`, `customers_affected`, `street_name`, `cityname`, `postcode`, `latitude`, `longitude`, `geom`, `uid`, `updated_at`.
2. **Poll the feed** — `getRecords` (`GET /catalog/datasets/outagecustomerlive/records`) with `limit` and `order_by`. Example query shape: `?order_by=customers_affected desc&limit=50`. Repeat for the other two dataset ids.
3. **Filter geographically** — `getRecords` with an ODSQL `where` clause over the `geom` field. Useful predicates: `in_bbox(geom, lat1, lon1, lat2, lon2)`, `within_distance(geom, geom'POINT(lon lat)', 5km)`. Filtering server-side is much cheaper than pulling the feed and filtering locally.
4. **Filter by area name** — `where=cityname="PENRITH"` or `where=postcode="2750"`. Use `getRecordsFacets` (`GET /catalog/datasets/outagecustomerlive/facets`) first to see which values actually occur; guessing a value returns an empty result set, not an error.
5. **Follow one incident** — `getRecord` (`GET /catalog/datasets/outagecustomerlive/records/{record_id}`) once you hold a record id from step 2.
6. **Aggregate** — `getRecords` with a `group_by` clause (e.g. `group_by=outage_cause_main&select=sum(customers_affected) as affected`) to summarise impact by cause or suburb in one call instead of paginating.

## Parsing warning

Every timestamp field on `outagecustomerlive` is typed `text`, not `date`. `start_date_time`, `end_date_time`, `est_restore_time` and `updated_at` come back as strings and must be parsed client-side. Do not assume ISO 8601 — inspect a live value before writing the parser, and do not use ODSQL date functions on these fields, because they are not date-typed in the index.

## Polling cadence

The datasets are described as refreshing every ten minutes. Poll at that cadence: 144 calls/day per feed, 432/day for all three, comfortably inside the 5,000/day domain quota. Polling faster returns identical data and burns a quota you share with every other consumer. There is no webhook, no event stream and no AsyncAPI — polling is the only option.

## Errors

- `429` with `{"errorcode": 10002, ...}` when the domain quota is exhausted. Back off until `reset_time` (00:00 UTC).
- `400` `ODSQLSyntaxError` on a malformed `where`; the message names the position and clause.
- `404` `NotFoundResource` on a bad dataset or record id — undeclared in the spec, real in practice.

See `errors/endeavour-energy-problem-types.yml` and `rate-limits/endeavour-energy-rate-limits.yml`.

## Notes

- All operations are GET and freely retryable.
- These datasets carry the Open Database Licence, so attribution is required if you republish.
- The customer-facing equivalent is the outage map at https://www.endeavourenergy.com.au/power-outages/outage-map — useful for sanity-checking what the feed reports.
