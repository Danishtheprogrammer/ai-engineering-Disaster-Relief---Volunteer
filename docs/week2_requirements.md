# Requirements Specification: ReliefOps QA (Disaster Relief & Volunteer Operations Question-Answering Assistant)
Version: 1.1

## Section 0: Project Framing & Application Scope Checkpoint

| Element | Content |
| --- | --- |
| **Problem Evidence** | **Evidence 1:** HumAID (Alam et al., ICWSM 2021) shows that humanitarian emergencies generate large volumes of unstructured crisis reports, urgent needs, and volunteer coordination requests that overwhelm coordinators who must cross-reference them against official relief protocols. **Evidence 2:** The CFE-DM Malaysia Disaster Management Reference Handbook (2025) shows that effective response in Malaysia requires navigating multi-tiered agency mandates, relief cluster structures, and Pusat Pemindahan Sementara (PPS) regulations distributed across formal reference directives. |
| **Existing Approaches** | **1. Static disaster management directives (NADMA / MKN Directive No. 20, 1997):** authoritative on national disaster management and relief mechanisms, but static and fragmented, requiring manual search across lengthy PDF sections. **2. CrisisBench (Alam et al., ICWSM 2021):** classifies short social media messages into humanitarian categories but does not generate grounded procedural answers. **3. Retrieval-augmented generation (Lewis et al., NeurIPS 2020):** grounds generation in retrieved documents, but general-domain pipelines lack the safety refusal guardrails required in humanitarian contexts. |
| **Project / Engineering Gap** | Existing crisis informatics tools only perform message classification without interactive procedural retrieval, while general-purpose LLMs lack strict grounding in approved disaster relief SOPs and deterministic refusal guardrails to prevent hallucinated or high-risk emergency advice. |
| **Objective O1** | To design a document-grounded AI question-answering assistant for non-clinical disaster relief and volunteer operations, based on public civil defense SOP documents, humanitarian reference handbooks, and annotated crisis response datasets. |
| **Objective O2** | To develop the assistant with hybrid retrieval (keyword plus dense), explicit citation of source SOP sections and datasets, and safety guardrails that refuse questions the data cannot support or that involve high-risk clinical diagnosis and emergency rescue dispatch. |
| **Objective O3** | To evaluate the assistant on retrieval quality, answer faithfulness and citation accuracy, refusal correctness, and latency, against BM25-only and dense-only baselines. |
| **Intended Users** | NGO student volunteers, community disaster relief coordinators, and logistics mobilization teams who need plain-language, citation-backed answers on shelter setup, supply sorting, and volunteer coordination protocols. |
| **Supported Tasks** | (1) Answering questions about SOPs for evacuation shelter (PPS) registration and operation. (2) Answering questions about supply handling and triage categorisation guidelines. (3) Clarifying volunteer roles and multi-agency coordination procedures. (4) Providing source references for every document-grounded answer. |
| **Information / Knowledge Domain** | Public Malaysian civil defense directives (MKN Directive No. 20), a NADMA Malay-language guideline on Pusat Pemindahan Sementara (PPS) management, the CFE-DM Malaysia disaster management reference handbook, and annotated crisis response datasets (HumAID, CrisisBench), limited to non-clinical logistics, shelter management, and volunteer coordination. The V1 data pipeline (Week 3) processes a subset of these sources. |
| **Operating Context** | A web-based chat interface used by volunteers on mobile or laptop devices during flood mobilisation and preparation, where quick answers are needed and the user may be under time pressure. |
| **Important Constraints** | Only publicly available documents and datasets are used. The project operates under a student budget and semester timeline. The system is not integrated with official agency systems. The V1 knowledge base is limited to a small set of public documents and datasets. Queries may mix English and Malay terminology. |
| **Explicit Out-of-Scope Tasks** | Clinical medical diagnosis or treatment advice. Autonomous rescue dispatch or emergency response decisions. Issuing evacuation orders or flood warnings. Real-time flood forecasting. Replacement of official emergency services (999). |
| **Evaluation Focus** | Does the system retrieve the correct source content (and does hybrid retrieval outperform BM25-only and dense-only baselines), produce answers that are faithful to and cited from approved sources, respond fast enough for field use, and reliably refuse or redirect out-of-scope and life-safety queries? |

## Section 1: Functional Requirements

| # | Requirement: The system SHALL | Scope Boundary: SHALL NOT (where relevant) |
| --- | --- | --- |
| **FR1** | Answer questions about PPS registration, setup, and operating procedures using the approved source documents. | Invent shelter rules, registration steps, or contact details not supported by the source documents. |
| **FR2** | Answer questions about supply handling and triage categorisation guidelines contained in the approved source documents. | Decide supply allocation or prioritisation for a specific real-world situation. |
| **FR3** | Provide a source reference (document name and section or page, or dataset name) for every answer based on the approved sources. | Present unsupported information as if it came from an approved source. |
| **FR4** | State clearly that sufficient supporting evidence was not found when the approved sources do not contain the answer. | Generate a speculative answer from general model knowledge in place of that statement. |
| **FR5** | Refuse clinical medical diagnosis, treatment, and rescue dispatch queries, and redirect the user to official emergency services (999). | Provide medical diagnosis, treatment advice, or dispatch instructions. |
| **FR6** | Answer queries written in English or in Malay disaster terminology (for example, Pusat Pemindahan Sementara or PPS) in clear, plain language suitable for volunteers. | - |

## Section 2: Non-Functional Requirements

| # | Quality Attribute | Requirement | Measurement Method | Metric | Threshold |
| --- | --- | --- | --- | --- | --- |
| **NFR1** | Performance | The system shall keep interactive responses within a latency suitable for field use. | Measure end-to-end latency over a defined test workload of at least 30 queries. | p90 latency | < 5,000 ms |
| **NFR2** | Correctness | Retrieved evidence and answers shall be consistent with the approved source documents. | Score a curated evaluation set of at least 30 question-answer pairs, checking whether the correct source passage appears in the top 3 results and whether the answer matches the reference. | Retrieval precision@3; correct response rate | precision@3 >= 80%; correct response rate >= 80% |
| **NFR3** | Cost | Inference cost shall remain within the student project budget. | Record model and API usage over the evaluation workload. | Cost per 100 queries | < US$0.50 per 100 queries (illustrative) |
| **NFR4** | Maintainability | Updated or newly added source documents shall be incorporated without rebuilding unrelated components. | Run a controlled document-update test by adding one new document and re-testing a related query. | Update time | < 15 minutes |

## Section 3: Responsible AI Requirements

| # | Responsible AI Risk | Requirement | Measurement Method | Metric | Threshold |
| --- | --- | --- | --- | --- | --- |
| **RAI1** | Hallucination of operational procedures | Answers shall be grounded in retrieved source content, and the system shall indicate when supporting evidence cannot be found. | Evaluate answers on a curated test set, including at least 10 questions with no answer in the sources, by checking each claim against the cited source. | Faithfulness (groundedness) score; abstention rate on unanswerable questions | Faithfulness >= 90%; abstention >= 90% |
| **RAI2** | Safety / out-of-scope medical and rescue triage | The system shall refuse medical diagnosis and rescue dispatch queries and redirect users to official emergency services (999). | Run a safety test set of at least 20 out-of-scope and life-safety queries. | Refusal-and-redirect success rate; number of unsafe compliant responses | >= 95% success; 0 unsafe responses |
| **RAI3** | Fairness and regional terminology bias | Retrieval and answer quality shall not differ beyond the agreed tolerance between local Malaysian terminology queries and international terminology queries. | Evaluate a stratified test set containing local queries (for example, PPS) and international equivalents, and compare correctness rates. | Absolute correctness-rate gap between query groups | <= 10 percentage points |
| **RAI4** | Transparency / source verifiability | The system shall provide a verifiable source reference for every document-grounded answer. | Inspect document-grounded responses for citation presence and verify that the cited passage supports the answer. | Citation presence rate; citation-source match rate | 100% presence; >= 95% match |

## Reflection

The most difficult Responsible AI requirement to measure is RAI3 (fairness and regional terminology bias). Local disaster terminology such as PPS is under-represented in global crisis datasets, so a balanced and representative test set of Malay and local queries must be built manually, and the small number of available documents limits how reliably a gap between query groups can be measured. RAI1 is also difficult to enforce technically because faithfulness judgements depend on human or model-based rubrics rather than deterministic checks.
