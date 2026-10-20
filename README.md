# Disaster Relief & Volunteer Operations QA Assistant

> A RAG-based assistant that helps NGO volunteers and community relief coordinators answer operational disaster response questions by retrieving evidence from official civil defense directives, humanitarian field handbooks, and annotated crisis response datasets to generate explainable, source-cited guidance.

## Problem Statement
During natural disasters such as seasonal monsoon floods, community volunteers and relief coordinators must rapidly verify Standard Operating Procedures (SOPs), evacuation shelter management rules, and supply triage categories, which are spread across lengthy policy PDFs and unstructured crisis logs. Standard keyword search or static portals require manual cross-referencing across documents and cannot synthesise multi-condition operational answers under time pressure. This project applies AI engineering, specifically retrieval-augmented generation (RAG) with source citation and safety refusal guardrails, to retrieve evidence from official disaster management documents and crisis benchmarks, generate explainable operational guidance, and ground every answer in retrieved sources to ensure field reliability and safety.

## Evidence of the Problem
- Humanitarian emergencies generate massive volumes of unstructured crisis reports, urgent needs, and volunteer coordination requests that overwhelm human coordinators trying to cross-reference them against official relief protocols. Alam, F., Qazi, U., Imran, M., & Ofli, F. (2021). HumAID: Human-annotated disaster incidents data from Twitter with deep learning benchmarks. *Proceedings of the International AAAI Conference on Web and Social Media (ICWSM)*, 15(1), 933–942. https://doi.org/10.48550/arXiv.2104.03090
- Effective disaster response and volunteer coordination in Malaysia require navigating multi-tiered agency mandates, relief cluster structures, and evacuation center (*Pusat Pemindahan Sementara*) regulations distributed across formal reference directives. Center for Excellence in Disaster Management and Humanitarian Assistance (CFE-DM). (2025). *Malaysia: Disaster management reference handbook* (December 2025). ReliefWeb. https://reliefweb.int/report/malaysia/malaysia-disaster-management-reference-handbook-december-2025

## Initial Related Work / Existing Approaches
- **Static Disaster Management Portals and Directives (NADMA Malaysia).** National Security Council Malaysia. (1997). *Directive No. 20 (Arahan MKN No. 20): Policy and mechanism of national disaster management and relief*. RCRC Resilience Southeast Asia. https://www.rcrc-resilience-southeastasia.org/wp-content/uploads/2017/12/1997_policy_and_mechanism_of_national_disaster_management_and_relief_national_security_council_directive.pdf. **Key limitation:** authoritative, but static and fragmented; volunteers must manually search lengthy PDF sections without interactive natural-language synthesis.
- **CrisisBench humanitarian information benchmark.** Alam, F., Sajjad, H., Imran, M., & Ofli, F. (2021). CrisisBench: Benchmarking crisis-related social media datasets for humanitarian information processing. *Proceedings of the International AAAI Conference on Web and Social Media (ICWSM)*, 15(1), 923–932. https://ojs.aaai.org/index.php/ICWSM/article/view/18115. **Key limitation:** focuses solely on classifying short social media messages into humanitarian categories rather than generating grounded, step-by-step procedural QA answers for volunteers.
- **Retrieval-Augmented Generation for knowledge-intensive NLP.** Lewis, P., Perez, E., Piktus, A., Petroni, F., Karpukhin, V., Goyal, N., ... & Kiela, D. (2020). Retrieval-augmented generation for knowledge-intensive NLP tasks. *Advances in Neural Information Processing Systems (NeurIPS)*, 33, 9459–9474. https://doi.org/10.48550/arXiv.2005.11401. **Key limitation:** general-domain RAG pipelines lack domain-specific safety refusal guardrails required in humanitarian contexts to block clinical medical diagnosis or autonomous life-safety dispatch.

## Engineering Gap / Limitation
Existing crisis informatics tools only perform message classification without interactive procedural retrieval, while general-purpose LLMs lack strict grounding in approved disaster relief SOPs and deterministic refusal guardrails to prevent hallucinated or high-risk emergency advice.

## Project Objectives
- **O1:** To design a document-grounded AI question-answering assistant for non-clinical disaster relief and volunteer operations, based on public civil defense SOP documents, humanitarian reference handbooks, and annotated crisis response datasets.
- **O2:** To develop the assistant with hybrid retrieval (keyword plus dense), explicit citation of source SOP sections and datasets, and safety guardrails that refuse questions the data cannot support or that involve high-risk clinical diagnosis and emergency rescue dispatch.
- **O3:** To evaluate the assistant on retrieval quality, answer faithfulness and citation accuracy, refusal correctness, and latency, against BM25-only and dense-only baselines.

## Target Users
NGO student volunteers, community disaster relief coordinators, and logistics mobilization teams who need plain-language, citation-backed answers on shelter setup, supply sorting, and volunteer coordination protocols.

## Data Sources
- **Source 1:** QCRI HumAID — Human-Annotated Disaster Incidents Dataset (rescue, volunteering, and donation coordination records). URL: https://huggingface.co/datasets/QCRI/HumAID-all
- **Source 2:** QCRI CrisisBench — Humanitarian Crisis Response & Actionable Needs Dataset. URL: https://huggingface.co/datasets/QCRI/CrisisBench-all-lang
- **Source 3:** National Security Council Malaysia (MKN) / NADMA. (1997). *Directive No. 20: Policy and mechanism of national disaster management and relief (Role of relief and voluntary bodies)*. URL: https://www.rcrc-resilience-southeastasia.org/wp-content/uploads/2017/12/1997_policy_and_mechanism_of_national_disaster_management_and_relief_national_security_council_directive.pdf
- **Source 4:** Center for Excellence in Disaster Management and Humanitarian Assistance (CFE-DM). (2025). *Malaysia: Disaster management reference handbook*. ReliefWeb. URL: https://reliefweb.int/report/malaysia/malaysia-disaster-management-reference-handbook-december-2025

## Weekly Progress
| Week | Focus | Deliverable | Status |
|------|-------|-------------|--------|
| W1 | System map + project scope | README + docs | ✅ |
