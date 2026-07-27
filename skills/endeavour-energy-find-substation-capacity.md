---
generated: '2026-07-27'
method: generated
name: Find distribution substation available capacity
description: Query spare capacity at Endeavour Energy distribution substations to assess whether the local network can host more solar, batteries or EV charging at a given location.
api: openapi/endeavour-energy-open-data-explore-api-v2-1-openapi.json
operations: [getDataset, getRecords, getRecordsFacets, exportRecords]
source: >-
  operationIds verified verbatim in
  openapi/endeavour-energy-open-data-explore-api-v2-1-openapi.json; field names
  read from the live schema of distribution-substation-available-capacity on
  2026-07-27.
---

# Find distribution substation available capacity

The most commercially useful dataset Endeavour Energy publishes. `distribution-substation-available-capacity` holds 30,500 records of spare capacity per distribution substation — the number that determines whether a proposed solar array, battery, heat pump or EV charger can connect without a network upgrade. It is published voluntarily, with no mandate compelling it, and it is queryable anonymously.

## Auth

None. Anonymous GET.

## Schema

`GET /catalog/datasets/distribution-substation-available-capacity` (`getDataset`) returns these fields:

| field | type | meaning |
|---|---|---|
| `objectid` | int | record key |
| `dsub` | text | distribution substation identifier |
| `avlbl_k` | double | available capacity (kVA) |
| `geo_shape` | geo_shape | substation supply-area polygon |
| `geo_point_2d` | geo_point_2d | point location |
| `skip_records` | text | platform field |

## Steps

1. **Read the schema** — `getDataset` (`GET /catalog/datasets/distribution-substation-available-capacity`). Confirm the field names before querying; this dataset was last modified 2026-02-04 and its shape can change.
2. **Find the substation serving a location** — `getRecords` (`GET /catalog/datasets/distribution-substation-available-capacity/records`) with an ODSQL geo predicate on `geo_shape` or `geo_point_2d`. Use `within_distance(geo_point_2d, geom'POINT(lon lat)', 2km)` to find nearby substations, or `in_bbox(geo_point_2d, lat1, lon1, lat2, lon2)` for an area sweep.
3. **Filter by headroom** — add a `where` on `avlbl_k` (for example `avlbl_k > 100`) and `order_by=avlbl_k desc` to rank sites by spare capacity. Combine both clauses in one call; do not fetch and filter client-side.
4. **Select only what you need** — `select=dsub,avlbl_k,geo_point_2d` keeps responses small. Geometry columns dominate the payload size on this dataset.
5. **Look up a specific substation** — `where=dsub="<identifier>"`. Use `getRecordsFacets` (`GET /catalog/datasets/distribution-substation-available-capacity/facets`) first if you need to see which `dsub` values exist.
6. **Take the whole layer** — `exportRecords` (`GET /catalog/datasets/distribution-substation-available-capacity/exports/{format}`) for GIS work. At 30,500 records this is within the paginated window's reach in principle, but `offset+limit < 10000` still caps `getRecords`, so a full pull must use the export path.

## Licence warning

This dataset declares **no licence** (`metas.default.license` is null). Unlike the poles and outage layers it carries no Open Database Licence grant. Verify reuse terms with Endeavour Energy before republishing or embedding it in a commercial product.

## Cross-check against the physical network

There is no foreign key linking a substation to the poles or conductors that hang off it. If you need the connected asset picture, join geospatially: export this layer plus `endeavourenergy_poles` and `conductors_hv_lv_sl_ug`, then intersect on geometry. See `data-model/endeavour-energy-data-model.yml`.

## Errors

- `400` `ODSQLSyntaxError` — most often a malformed geo predicate. Check the argument order: `within_distance(field, geom'POINT(lon lat)', distance)` takes longitude first.
- `404` `NotFoundResource` on a mistyped dataset id.
- `429` when the shared 5,000/day domain quota is exhausted.

## Notes

- All operations are GET and freely retryable.
- Endeavour Energy also publishes a human-facing network capacity map at https://www.endeavourenergy.com.au/our-network/network-demand-and-capacity/network-capacity-map — useful for validating what a query returns.
- This is network hosting capacity, not a connection approval. A real connection still goes through Endeavour Energy's connections process; the API tells you where it is plausible, not where it is permitted.
