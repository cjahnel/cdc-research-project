# NOAA tornado extract, 1950–2025

Extracted September 26, 2026 from the NOAA NCEI Storm Events bulk CSV archive.

## Contents

| File | Rows | Meaning |
|---|---:|---|
| tornado_details_1950_2025.csv | 80,318 | NOAA event records classified as Tornado |
| tornado_fatalities_1950_2025.csv | 2,826 | Fatalities-table rows linked to retained event IDs |
| tornado_locations_1950_2025.csv | 72,146 | Locations-table rows linked to retained event IDs |
| annual_data_quality.csv | 76 | Annual counts and basic completeness checks |
| fatality_reconciliation_exceptions.csv | 573 | Events whose direct-death total differs from linked direct-fatality row count |
| source_manifest.json | 228 files | Exact source URLs, creation versions, fields, sizes and SHA-256 hashes |
| extraction_summary.json | — | Aggregate extraction statistics |
| verification.json | — | Checks against the saved extracts |

## Extraction rules

Downloaded all three tables for each source year 1950–2025. Where multiple source versions existed, selected the greatest creation-date suffix. Excluded the partial 2026 source year. The selected 2025 details contain tornado records in all 12 months, but this is not proof that reporting or revisions are complete.

Filtered details by EVENT_TYPE, trimming whitespace and comparing case-insensitively to Tornado. Retained all original columns and values, including every intensity rating and all geographic areas present in NOAA. No contiguous-US filter has been applied. Added SOURCE_YEAR and SOURCE_FILE for provenance. Blank values remain blank. CSV reserialization may change quoting, but source strings are not cleaned or imputed. Original compressed source files are retained in the working cache.

Retained fatalities and locations by membership in the full set of tornado EVENT_ID values across selected years. Kept the three tables separate. EVENT_ID is unique across the 80,318 retained details records. Joining both child tables directly can multiply rows and overcount casualties or events; aggregate each child table to EVENT_ID first when an event-level table is needed. No cross-segment tornado linkage or deduplication has been attempted. EPISODE_ID is not a unique tornado identifier.

Read identifier columns as strings. Dates, damage suffixes, ratings, and NOAA measurement units are preserved. SOURCE_YEAR identifies the source file, not a newly inferred event date.

## Initial findings

- 2,012,975 details records scanned; 80,318 retained as Tornado. These are event/segment records, not a count of unique physical tornadoes.
- 43,236 records have an F/EF1–5 rating. 3,599 have an unknown rating: 1,971 blank and 1,628 EFU. Rating harmonization has not modified the extract.
- 1,176 records have missing or invalid beginning points; 25,933 have missing or invalid ending points. The basic validity check requires numeric coordinates in global latitude/longitude bounds with neither coordinate zero. It does not establish that a point lies in the correct county or follows a plausible path.
- Of 37,543 pre-1996 records, 25,787 (68.7%) fail the ending-point check. Of 42,775 records from 1996–2025, 146 (0.34%) fail it. All pre-1996 records also have blank EPISODE_ID in this extract. This is a material historical schema/coverage break.
- 42,761 events have at least one linked location record. Missing child records are not evidence that an event did not occur or had no casualties.
- 573 event-level discrepancies exist between DEATHS_DIRECT and linked FATALITY_TYPE=D row counts: 554 before 1996 and 19 from 1996 onward. Historical fatalities-table coverage does not support treating its row count as the complete historical death toll. Preserve and investigate the discrepancies; do not overwrite either source.
- Four FATALITY_ID values are reused across different events in 2005 and 2013. The pair (EVENT_ID, FATALITY_ID) is unique in the retained table. These are different records, so no rows were dropped. Location pairs (EVENT_ID, LOCATION_INDEX) are also unique.
- The details table reports 6,349 direct deaths and 99,284 direct injuries across retained records. These are raw source sums, not reconciled estimates. Fatalities-table totals include both direct and indirect records, whereas the reconciliation compares direct deaths only.

## Implications for the project

Use these files as an auditable starting extract. Before drawing geographic trends, choose a segment-count or tornado-day definition, resolve path geometry and historical coverage, apply the intended geographic scope, and examine reporting/intensity changes. For early history, start-point or county analyses may be more feasible than reconstructed full paths, with explicit limitations. A modern path-based analysis can be evaluated separately.

No hazard trends, projections, vulnerability scores, or modeling claims have been produced at this stage.

## Sources

- NOAA archive: https://www.ncei.noaa.gov/pub/data/swdi/stormevents/csvfiles/
- NOAA field dictionary: https://www.ncei.noaa.gov/pub/data/swdi/stormevents/csvfiles/Storm-Data-Bulk-csv-Format.pdf
- NOAA bulk-file notes: https://www.ncei.noaa.gov/pub/data/swdi/stormevents/csvfiles/README

Exact source-file URLs and hashes are in source_manifest.json. NOAA can revise older years, so retain this manifest with the analysis.
