# Week 3 Pipeline Decisions: ReliefOps QA

## 1. Requirements addressed

| Requirement ID | Data engineering decision that supports it |
| --- | --- |
| FR1 | Two approved PDF sources (S01, S02) containing evacuation centre and PPS procedure text are ingested and prepared. |
| FR3 / RAI4 | Every prepared record keeps its source file, SHA-256 checksum and PDF page number, so a later answer can cite document and page. |
| RAI3 / FR6 | One English source (S01) and one Malay source (S02) are prepared together, because they use disjoint vocabulary for the same concept. |
| NFR4 | Sources are identified by SHA-256, and the pipeline is repeatable per source, so a new or updated document can be re-ingested and verified without rebuilding unrelated components. |
| FR5 / RAI2 (supporting) | S02 chunks that contain COVID-19 health-control content are tagged `covid_health`, so later retrieval can avoid presenting them as clinical advice. |

## 2. Selected sources

| Source | File | Description | Inspection result |
| --- | --- | --- | --- |
| S01 | mkn_directive_20_1997.pdf | MKN Directive No. 20 (1997), English | 26 pages, 55,055 characters, no pages without extractable text |
| S02 | nadma_garis_panduan_pps_my.pdf | NADMA Garis Panduan Pengurusan PPS semasa COVID-19, Malay | 5 pages, 7,586 characters, no pages without extractable text |

HumAID, CrisisBench and the CFE-DM handbook are listed in the Week 2 knowledge domain but are not processed in the Week 3 V1 pipeline, because they do not provide procedural text that can be cited at page level.

## 3. Part B comparison and evidence

All three configurations used the same extracted text and a 500-character target size.

| Approach A | Approach B | Observed evidence | Selected approach and trade-off |
| --- | --- | --- | --- |
| Fixed-size chunks (500 characters, whole document) | Paragraph-aware chunks (lab method, whole document) | S01: fixed gave 111 chunks, of which 66 boundaries cut inside a word and only 6% of chunks ended at punctuation; paragraph-aware gave 136 chunks with 33% ending at punctuation but a 5-character minimum chunk. S02: fixed gave 16 chunks with 9 mid-word cuts and 12% ending at punctuation; paragraph-aware gave 18 chunks with 56% ending at punctuation. Neither method records a page number. | Paragraph-aware is better than fixed, because fixed chunking splits words and registration clauses (for example the Appendix M duty list in S01). Neither satisfies FR3/RAI4, because the page is None. |
| Paragraph-aware chunks (whole document) | Page-aware paragraph chunks with cleaning, line reflow and contents-page exclusion (selected) | S01: 138 chunks in the final export (142 before the second contents page was excluded), 70% ending at punctuation (measured before that exclusion), and 9 stray "E" encoding artefacts reduced to 0. S02: 18 chunks, 78% ending at punctuation, and the page 4 flowchart kept whole as one 1,021-character chunk. Every record carries a PDF page number, and the S01 victim registration duties appear in a page 25 record. | Page-aware chunking is selected. Benefit: each record can be cited at page level (FR3/RAI4) and chunks follow list items and headings. Accepted limitation: a paragraph that continues across a page break becomes two chunks. |

## 4. Chosen preparation configuration

- Extraction with pypdf, page by page.
- Unicode NFKC normalisation (removes the "ﬁ" ligature found in "Deﬁnition").
- Regex repair of stray "E" characters that replaced a closing quotation mark and space (for example `post-mortemEis` becomes `post-mortem’ is`).
- Repair of spaced hyphens (for example `COVID -19`).
- Line reflow: wrapped lines are joined into paragraphs, while numbered items, lettered items and headings stay separate, because pypdf returns almost no blank lines.
- S01 contents pages 1 and 2 excluded (page 1 detected automatically, page 2 listed explicitly because pypdf returns it as one long line).
- Page-aware paragraph chunks with a 500-character target size.
- S02 page 4 (flowchart) kept as one whole-page chunk, because its extracted reading order is unreliable.
- `covid_health` flag on chunks that match COVID-19 health-control terms (0 of 138 S01 chunks and 13 of 18 S02 chunks).

## 5. Accepted limitations

- Paragraphs that continue across a page break are split into two chunks.
- Cleaning rules are heuristic. The stray-E repair only handles the pattern with an opening quotation mark; the post-cleaning count was 0, but other artefacts may exist.
- Some very short chunks remain (S01 minimum 21 characters), caused by page-end and heading fragments.
- Page 3 of S01 is an abbreviation glossary and is kept as ordinary text, so acronym queries may retrieve it.
- The S02 page 4 flowchart text is not in reliable reading order, so it is stored whole and not interpreted.
- Charts in the S01 appendices contain no extractable text and are not represented.
- 13 of 18 S02 chunks (72%) concern COVID-19 health controls, which are outside the non-clinical scope of the assistant.
- FR2 (supply triage categories) is only partly supported; neither source defines triage categories.
- Two documents (about 62,600 characters) are too few for a reliable fairness estimate for RAI3.

## 6. ETL or ELT decision

ETL. Cleaning, normalisation and chunking are applied before the prepared records are stored, because raw PDF text is not usable as stored data and the Part B evidence shows that the extracted text needs repair and structuring first. The raw PDFs are kept unchanged in `data/raw` and identified by SHA-256, so the transformation can be re-run if a rule changes.

## 7. Batch or streaming rationale

Batch. The corpus is two static documents, and updates are infrequent (NFR4 requires adding a document within minutes, not in real time). There is no streaming source, and a streaming design would add complexity without a requirement that needs it.

## 8. Stage contracts and evidence

| Stage | Contract | Failure response | Evidence from the executed run |
| --- | --- | --- | --- |
| Ingest | Chosen source exists and is permitted | Stop with FileNotFoundError and re-download from the URL recorded in the data inventory | `resolve_source()` found both files; SHA-256 recorded for S01 (1f9b2faf...36ee4) and S02 (db47e10e...a6e87) |
| Inspect | Source is readable and has extractable text | Stop if the document has no extractable text; list pages without text in the inspection record | S01: 26 pages, 55,055 characters; S02: 5 pages, 7,586 characters; no pages without text |
| Prepare | Page-aware method produces non-empty chunks with page numbers | Stop if no chunks are produced | S01: 138 records (pages 3 to 26); S02: 18 records (pages 1 to 5) |
| Validate | Output files exist, are non-empty and have fingerprints | Stop for missing or empty output | Output sizes 91,640 bytes (S01) and 12,677 bytes (S02); SHA-256 stored in each pipeline manifest |
| Record lineage | Each source links to its exact output | Stop or report a missing mapping | `pipeline_manifest.json` per source; Part B `week3_manifest.json` with 2 verified entries |
| Repeatability (Cell 9) | A rerun gives identical fingerprints and mappings | Fail the check and investigate the changed rule | PASS for documents_s01 and documents_s02 |

## 9. Output locations

- Part B prepared output: `data/processed/prepared_text.jsonl` (156 records, 104,317 bytes) and `data/week3_manifest.json`.
- Part C outputs: `data/pipeline_v1/documents_s01/` and `data/pipeline_v1/documents_s02/`, each containing `prepared_text.jsonl` and `pipeline_manifest.json`.
- Raw PDFs remain in `data/raw` (Colab) and are not committed.

## 10. Differences between Part B and Part C

Part C runs the same page-aware configuration as Part B, but one run per source (the common pipeline utility accepts one source per run), so each source has its own output folder and `pipeline_manifest.json`. The concatenation of the two Part C outputs is identical to the Part B export (verified: True), so there is no difference in content.

## 11. Provenance and access caveats

- Source versions are not verified. S01 is the 1997 text, which was later revised (2012) and is under further review. S02 has no printed date and refers to COVID-19 movement control orders.
- Access rights are not verified. Both documents are public government publications, and their redistribution terms are not stated. The raw PDFs are not committed; the prepared text records contain extracts from both documents and should be shared only after this is checked.
- The original host of S01 (rcrc-resilience-southeastasia.org) is no longer available, and the copy hosted by AIPA Lync is used.
- Data quality, fitness-for-purpose and Responsible AI assessment of these records are deferred to Topic 4.
