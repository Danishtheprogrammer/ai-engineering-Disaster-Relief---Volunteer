# Disaster Relief & Volunteer Operations QA Assistant

> A RAG-based assistant that helps NGO volunteers and community relief coordinators answer operational disaster response questions by retrieving evidence from official civil defense directives and guidelines to generate explainable, source-cited guidance.

## Problem Statement
During natural disasters such as seasonal monsoon floods, community volunteers and relief coordinators must rapidly verify Standard Operating Procedures (SOPs), evacuation shelter management rules, and supply triage categories, which are spread across lengthy policy PDFs and unstructured crisis logs. Standard keyword search or static portals require manual cross-referencing across documents and cannot synthesise multi-condition operational answers under time pressure. This project applies AI engineering, specifically retrieval-augmented generation (RAG) with source citation and safety refusal guardrails, to retrieve evidence from official disaster management documents, generate explainable operational guidance, and ground every answer in retrieved sources to ensure field reliability and safety.

## Evidence of the Problem
- Humanitarian emergencies generate massive volumes of unstructured crisis reports, urgent needs, and volunteer coordination requests that overwhelm human coordinators trying to cross-reference them against official relief protocols. Alam, F., Qazi, U., Imran, M., & Ofli, F. (2021). HumAID: Human-annotated disaster incidents data from Twitter with deep learning benchmarks. *Proceedings of the International AAAI Conference on Web and Social Media (ICWSM)*, 15(1), 933–942. https://doi.org/10.48550/arXiv.2104.03090
- Effective disaster response and volunteer coordination in Malaysia require navigating multi-tiered agency mandates, relief cluster structures, and evacuation center (*Pusat Pemindahan Sementara*) regulations distributed across formal reference directives. Center for Excellence in Disaster Management and Humanitarian Assistance (CFE-DM). (2025). *Malaysia: Disaster management reference handbook* (December 2025). ReliefWeb. https://reliefweb.int/report/malaysia/malaysia-disaster-management-reference-handbook-december-2025

## Initial Related Work / Existing Approaches
- **Static Disaster Management Portals and Directives (NADMA Malaysia).** National Security Council Malaysia. (1997). *Directive No. 20 (Arahan MKN No. 20): Policy and mechanism of national disaster management and relief*. AIPA Lync Digital Law Library. https://aipalync.org/document/national-security-council-directive-no-20-policy-and-mechanism-of-national-disaster-management-and-relief. **Key limitation:** authoritative, but static and fragmented; volunteers must manually search lengthy PDF sections without interactive natural-language synthesis.
- **CrisisBench humanitarian information benchmark.** Alam, F., Sajjad, H., Imran, M., & Ofli, F. (2021). CrisisBench: Benchmarking crisis-related social media datasets for humanitarian information processing. *Proceedings of the International AAAI Conference on Web and Social Media (ICWSM)*, 15(1), 923–932. https://ojs.aaai.org/index.php/ICWSM/article/view/18115. **Key limitation:** focuses solely on classifying short social media messages into humanitarian categories rather than generating grounded, step-by-step procedural QA answers for volunteers.
- **Retrieval-Augmented Generation for knowledge-intensive NLP.** Lewis, P., Perez, E., Piktus, A., Petroni, F., Karpukhin, V., Goyal, N., ... & Kiela, D. (2020). Retrieval-augmented generation for knowledge-intensive NLP tasks. *Advances in Neural Information Processing Systems (NeurIPS)*, 33, 9459–9474. https://doi.org/10.48550/arXiv.2005.11401. **Key limitation:** general-domain RAG pipelines lack domain-specific safety refusal guardrails required in humanitarian contexts to block clinical medical diagnosis or autonomous life-safety dispatch.

## Engineering Gap / Limitation
Existing crisis informatics tools only perform message classification without interactive procedural retrieval, while general-purpose LLMs lack strict grounding in approved disaster relief SOPs and deterministic refusal guardrails to prevent hallucinated or high-risk emergency advice.

## Project Objectives
- **O1:** To design a question-answering assistant for disaster relief volunteers in Malaysia, based on public official directives and guidelines.
- **O2:** To develop the assistant using hybrid retrieval, source citations, and safety guardrails that refuse medical diagnosis and rescue dispatch queries.
- **O3:** To evaluate the assistant on retrieval quality, answer faithfulness, citation accuracy, refusal accuracy, and response latency.

## Target Users
NGO student volunteers, community disaster relief coordinators, and logistics mobilization teams who need plain-language, citation-backed answers on shelter setup, supply sorting, and volunteer coordination protocols.

## Data Sources
- **Source 1:** QCRI HumAID: Human-Annotated Disaster Incidents Dataset (rescue, volunteering, and donation coordination records). URL: https://huggingface.co/datasets/QCRI/HumAID-all *(not processed in the Week 3 V1 pipeline)*
- **Source 2:** QCRI CrisisBench: Humanitarian Crisis Response & Actionable Needs Dataset. URL: https://huggingface.co/datasets/QCRI/CrisisBench-all-lang *(not processed in the Week 3 V1 pipeline)*
- **Source 3:** National Security Council Malaysia (MKN). (1997). *Directive No. 20: Policy and mechanism of national disaster management and relief (Role of relief and voluntary bodies)*. URL: https://aipalync.org/document/national-security-council-directive-no-20-policy-and-mechanism-of-national-disaster-management-and-relief *(used in the V1 pipeline)*
- **Source 4:** Center for Excellence in Disaster Management and Humanitarian Assistance (CFE-DM). (2025). *Malaysia: Disaster management reference handbook*. ReliefWeb. URL: https://reliefweb.int/report/malaysia/malaysia-disaster-management-reference-handbook-december-2025 *(not processed in the Week 3 V1 pipeline)*
- **Source 5:** National Disaster Management Agency (NADMA). *Garis Panduan Pengurusan PPS semasa COVID-19* (Malay-language PPS management guideline). URL: https://www.nadma.gov.my/images/nadma/documents/PPSCOVID19/Garis_Panduan_PPS.pdf *(used in the V1 pipeline)*

## Repository Structure
```
docs/        Weekly requirements, evaluation criteria, architecture and pipeline documents
notebooks/   week3_pipeline.ipynb (data pipeline)
data/        Prepared records and lineage manifests (raw PDFs are not committed)
```

## Weekly Progress
| Week | Focus | Deliverable | Status |
|------|-------|-------------|--------|
| W1 | System map + project scope | README + docs | ✅ |
| W2 | Requirements, evaluation criteria, V1 architecture | docs/week2_requirements.md, docs/week2_evaluation_criteria.md, docs/week2_architecture_v1.png, docs/week2_architecture_decisions.md | ✅ |
| W3 | Data engineering: V1 data pipeline (Sources 3 and 5) | notebooks/week3_pipeline.ipynb, docs/week3_data_inventory.md, docs/week3_pipeline_diagram.png, docs/week3_pipeline_decisions.md, data/week3_manifest.json | ✅ |
