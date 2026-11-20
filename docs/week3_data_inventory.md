# Week 3 Data Inventory

Project: ReliefOps QA | Pathway: A (PDF) | Environment: Google Colab | Pipeline version: week3-v1

## 1. Purpose

This inventory records the real project sources used by the V1 data pipeline. No synthetic practice dataset is used.

## 2. Requirements Served

| Requirement | How the data supports it |
| --- | --- |
| FR1 | Official directives supply the content for grounded answers on shelter operations and volunteer coordination. |
| FR2 | Partly supported. Registration and supply guidance exist, but there are no explicit supply triage categories. |
| RAI3 | The corpus uses different terms for the same concept ("evacuation centre", "PPS", "Pusat Pemindahan"), which tests bilingual and hybrid retrieval. |

Please check the requirement IDs against `docs/week2_requirements.md` before submission.

## 3. Source Register

| ID | Source | Language | Pages (kept/total) | Characters kept | Access | SHA-256 |
| --- | --- | --- | --- | --- | --- | --- |
| S01 | MKN Directive No. 20 (1997) | English | 24/26 | 52,722 | Public | 1f9b2faf796291454d741a1b7d66abd3cebf7ed3132f2bef7ecda675ac336ee4 |
| S02 | NADMA Garis Panduan PPS | Malay | 5/5 | 7,423 | Public (nadma.gov.my) | db47e10e04865c87fc6b1489f16d7ba3321cbf081e0919db6dcc7a380cd2aa87 |
| S03 | Arahan NADMA No. 1 (2024) | Malay | 70/80 | 247,001 | TERHAD, used with permission | cc648944894c2ea079af5eb8fec571d3b349250b55461ecfef4dbf3c1a99c24f |
| S04 | JKM Peraturan Tetap Operasi Pengurusan Bencana (2018) | Malay | 48/88 | 69,601 | Used with permission | f3ec461ea3ad3ed0d3495db73e8065ac2a300b57fe66eb90fd6e0cbf61945232 |

## 4. Inspection Evidence

- S01: the contents pages (1 and 2) are excluded. Typographic ligatures are normalised, and stray "E" characters left by closing quotes are repaired. The directive is flagged `superseded`.
- S02: page 4 is a flowchart and is kept as one whole-page chunk. Pages 1 to 3 describe COVID controls, so the source is flagged `covid_era`.
- S03: "TERHAD" stamps, print artefacts of the form "teks NADMA New.indd N" and page numbers are removed. Contents and divider pages are excluded.
- S04: running page footers are removed. Contents, forms and annexes are excluded by explicit page lists.

## 5. Access and Provenance Caveats

- S01 and S02 are public documents.
- S03 and S04 were used with permission from NADMA and the course lecturer. [add reference to permission message]. Their text is not redistributed in this repository.
- Raw PDFs and the prepared text of S03 and S04 are excluded by `.gitignore`. Only counts, hashes and manifests are committed.
- Source version and access rights are recorded as "Not verified" in the manifests, which is a prompt for the Topic 4 review.

## 6. Sources Considered and Not Used

| Source | Reason |
| --- | --- |
| RELA Act (Akta 752) | Covers RELA membership and powers. It has no shelter or disaster content. |
| Ministry of Health Disaster Management Plan | About 60 pages are clinical, which conflicts with the non-clinical scope. It carries its own copyright notice. |
| NADMA ETS 8 volunteer slides, slides 53 to 66 | Optional extra (S05). Not part of the executed V1 run. |

## 7. Known Limitations

- S03 produces 65% of all records (638 of 982), which may bias retrieval. This is to be handled in the retrieval stage.
- S01 and S02 contain outdated or COVID-era content. These are flagged, not deleted.
- Sources may give different values for the same procedure (for example food-label times), so answers must cite the source.
