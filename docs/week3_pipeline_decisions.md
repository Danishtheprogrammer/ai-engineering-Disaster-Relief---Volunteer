# Week 3 Pipeline Decisions

## 1. Requirements and Selected Sources

Requirements: FR1, FR2 (partial), RAI3. Sources: S01, S02, S03, S04 (see `docs/week3_data_inventory.md`). Pathway: A, PDF.

## 2. Part B Comparison and Evidence

Three configurations were compared on the same cleaned text of all four sources (chunk size 500 characters).

| Source | Fixed | Paragraph-aware | Page-aware paragraph | Avg length (page-aware) | Max length (page-aware) |
| --- | --- | --- | --- | --- | --- |
| S01 | 106 | 134 | 139 | 378 | 500 |
| S02 | 15 | 19 | 19 | 389 | 1,139 |
| S03 | 495 | 614 | 638 | 386 | 500 |
| S04 | 140 | 176 | 186 | 373 | 500 |
| Total | 756 | 943 | 982 | | |

Fixed chunks cut sentences and numbered clauses in the middle and carry no page number. Paragraph-aware chunks keep clause boundaries but give no reliable page reference. The page-aware version keeps paragraph boundaries inside each page, so every chunk has a page number for citation. The one chunk above 500 characters is the whole-page S02 flowchart, which is kept whole on purpose.

## 3. Chosen Configuration

- Method: page-aware paragraph chunking, 500 characters, with pypdf extraction.
- Cleaning: NFKC normalisation, repair of stray quote characters and spaced hyphens, per-source noise removal and line reflow.
- Page rules: contents pages, form pages and annexes are skipped by explicit page lists. S02 page 4 is a single whole-page chunk.
- Flags: `superseded` (S01, 139 records), `covid_era` (S02, 19 records) and `covid_health` on chunks that match COVID terms (14 records).
- Output: 982 records. Each has record ID, source ID, file name, source SHA-256, page, chunk index, text, processing label and flags.

## 4. Accepted Limitations

1. The splitter is rule-based, not semantic. A related list may be split across two chunks.
2. Page-based skip lists are manual and apply only to these documents.
3. COVID content is flagged but not removed. Later retrieval should exclude `covid_health` chunks unless the query mentions COVID.
4. The flowchart on S02 page 4 is kept as text only. Its arrows and layout are lost.
5. S03 dominates the corpus (638 of 982 records).

## 5. ETL or ELT

ETL. The restricted PDFs must be cleaned, stripped of access markings and noise, and validated before storage and use, so transformation happens first. The prepared output is small (about 0.65 MB), so loading is not a bottleneck.

## 6. Batch or Streaming

Batch. The sources are static documents that change only when an authority issues a new version. A re-run on new files is enough. No real-time source is required.

## 7. Stage Contracts

| Stage | Contract | Failure response |
| --- | --- | --- |
| Ingest | The chosen source exists and its use is permitted. | Stop with an error. |
| Inspect | The PDF is readable and has extractable text. | Stop if no text is extracted. |
| Prepare | The chosen configuration produces non-empty chunks. | Stop and report. |
| Validate | Output files exist, are non-empty and have fingerprints. | Stop for missing or empty output. |
| Record lineage | Each source links to its exact output. | Stop and report missing mapping. |

## 8. Output Locations

| Item | Path |
| --- | --- |
| Part B prepared records | `data/processed/prepared_text.jsonl` |
| Part B lineage manifest | `data/week3_manifest.json` |
| Part C outputs | `data/pipeline_v1/documents_s01/` to `documents_s04/` (one folder per source) |
| Part C manifests | `data/pipeline_v1/documents_<id>/pipeline_manifest.json` |

Prepared text for S03 and S04 is not committed (see Section 9).

## 9. Provenance and Access Caveat

S03 and S04 are marked restricted and were used with permission from NADMA and the course lecturer. [add reference to permission message]. Raw files and prepared text for these sources stay outside the public repository. The repository holds hashes, counts, manifests and execution evidence only.

## 10. Repeatability Evidence

Cell 9 re-ran all four pipelines. Source fingerprints, output fingerprints and mappings matched, and each source reported PASS. Cell 8A-check confirmed that Part C output equals Part B output for every source (982 records). This is a smoke test, not full cross-environment reproducibility.
