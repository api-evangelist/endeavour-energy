---
generated: '2026-07-27'
method: generated
name: Bulk export the network asset layers
description: Pull Endeavour Energy's poles, conductors and other network asset datasets in full via the export endpoints, which is the only way past the 10,000-row query window.
api: openapi/endeavour-energy-open-data-explore-api-v2-1-openapi.json
operations: [getDataset, listDatasetExportFormats, exportRecords, exportRecordsCSV, exportRecordsParquet, exportRecordsGPX, exportCatalogDCAT]
source: >-
  operationIds verified verbatim in
  openapi/endeavour-energy-open-data-explore-api-v2-1-openapi.json; pagination
  bounds read from the spec's limit/offset parameter descriptions; record
  counts read from the live catalogue on 2026-07-27.
---

# Bulk export the network asset layers

The three physical-asset datasets are large — 808,500 conductor segments, 440,725 poles, 317,031 other assets. **None of them can be retrieved through `getRecords`.** The paginated window is capped at `offset + limit < 10000`, so paginating stops at 10,000 rows no matter how patient you are. The export endpoints exist precisely for this and have no record limit.

## Auth

None. Anonymous GET.

## The bulk datasets

| dataset_id | records | licence |
|---|---|---|
| `conductors_hv_lv_sl_ug` | 808,500 | none declared |
| `endeavourenergy_poles` | 440,725 | Open Database Licence |
| `networkassets_otherassets` | 317,031 | Open Database Licence |
| `distribution-substation-available-capacity` | 30,500 | none declared |

## Steps

1. **Read the schema first** — `getDataset` (`GET /catalog/datasets/{dataset_id}`) so you know the columns before you download hundreds of megabytes. `endeavourenergy_poles` fields: `g3e_fid`, `feature_type`, `ug`, `cm`, `lv`, `sl`, `tr`, `hv`, `usage`, `asset_num`, `state`, `geom`. `distribution-substation-available-capacity` fields: `objectid`, `dsub`, `avlbl_k`, `geo_shape`, `geo_point_2d`, `skip_records`.
2. **Check available formats** — `listDatasetExportFormats` (`GET /catalog/datasets/{dataset_id}/exports`).
3. **Export.** Pick the operation that matches your target:
   - `exportRecordsParquet` (`GET /catalog/datasets/{dataset_id}/exports/parquet`) — best choice for anything analytical at this scale.
   - `exportRecordsCSV` (`GET /catalog/datasets/{dataset_id}/exports/csv`) — note that v2.1 emits a BOM by default.
   - `exportRecordsGPX` (`GET /catalog/datasets/{dataset_id}/exports/gpx`) — for the geometry-bearing layers.
   - `exportRecords` (`GET /catalog/datasets/{dataset_id}/exports/{format}`) — for any other format, including GeoJSON.
4. **Narrow before you export.** The export endpoints accept the same ODSQL clauses as `getRecords`. A `where` and a `select` on the export call is far better than downloading 808,500 rows and filtering locally — for example, export only the substations with real spare capacity rather than all 30,500 records.
5. **Take the catalogue itself** — `exportCatalogDCAT` (`GET /catalog/exports/dcat`) returns the whole catalogue as RDF/XML (DCAT), which is what to hand a data-catalogue tool rather than scraping the portal.

## Licence check — do this before you republish

`conductors_hv_lv_sl_ug` and `distribution-substation-available-capacity` declare **no licence** in `metas.default.license`. Together with `distribution-district` that is 839,001 records with no stated reuse terms. The Open Database Licence datasets require attribution and share-alike. Read `metas.default.license` from `getDatasets` and record it alongside whatever you extract.

## Rate limits

An export is a single call, so it costs one unit of the 5,000/day domain quota — dramatically cheaper than the 80,000 paginated calls it would replace (and which the window forbids anyway). Do not schedule exports more often than the data changes: `endeavourenergy_poles` was last modified 2026-03-17, `distribution-substation-available-capacity` 2026-02-04. Poll `metas.default.modified` from `getDatasets` and re-export only when it moves.

## Errors

- `400` `ODSQLSyntaxError` on a malformed `where`/`select` in the export query.
- `404` `NotFoundResource` on a bad `dataset_id`.
- `429` when the shared domain quota is gone — back off to 00:00 UTC.

## Notes

- Exports are GET and idempotent; a failed download can be retried safely.
- Six of the eight datasets carry `geo_point_2d` or `geo_shape`. There are no foreign keys between datasets, so any cross-layer analysis (which conductors hang off which poles, which substations sit in which district) has to be done geospatially after export, or server-side with the ODSQL geo predicates. See `data-model/endeavour-energy-data-model.yml`.
