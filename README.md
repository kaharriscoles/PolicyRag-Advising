# PolicyRag-Advising
# PolicyRag: Evaluating Hybrid Retrieval and Constrained Generation for Academic Advising

A reproducible technical artifact for evaluating sparse lexical search (BM25), dense vector retrieval (BGE-Small in FAISS), and hybrid Reciprocal Rank Fusion (RRF) over official university Computer Science course catalog policies.

---

## 1. System Requirements
* **Python**: Version 3.10 or higher
* **Hardware**: Standard 8-core CPU, minimum 8 GB RAM (12 GB recommended)
* **GPU**: None required (runs fully on CPU runtimes or Google Colab)

---

## 2. Dependencies and Installation
To install the required Python packages, run:

pip install -q sentence-transformers faiss-cpu rank-bm25 beautifulsoup4 requests transformers accelerate

3. Data Collection and ProvenanceSource: Official VCU Bulletin Computer Science (CMSC) Course Descriptions
   URL: https://bulletin.vcu.edu/azcourses/cmsc/
   Snapshot Date: October 2026
   Ingestion Process: The notebook parses all 84 active CMSC course entries directly from the bulletin HTML.
   Each record extracts:Course code (normalized to uppercase with single spacing)
   Course title, credits, and verbatim prerequisite requirements
   Direct URL anchor links for catalog verification

4. Retrieval and Generation PipelineLexical Baseline (BM25): Word-tokenized, lowercased corpus indexed using BM25Okapi.
   Dense Vector Search: Course passages encoded into 384-dimensional dense vectors using BAAI/bge-small-en-v1.5 and indexed via faiss.IndexFlatIP for cosine similarity.
   Hybrid RRF: Combines BM25 and dense retrieval rankings using Reciprocal Rank Fusion (k = 60):Score = (1 / (60 + BM25_Rank)) + (1 / (60 + Dense_Rank))
   Constrained Generation: Uses google/flan-t5-base with strict prompt constraints requiring source catalog URL citations and explicit refusal triggers on out-of-scope queries.

5. How to Run and ReproduceRunning in Google Colab (Recommended) 1.Open PolicyRag_Benchmark.ipynb in Google Colab. 2.Select Runtime -> Run all. 3.The notebook will scrape the catalog, build both search indices, run the 40-question benchmark, output retrieval metrics, and run sample generation outputs. Running LocallyBashpython pipeline.py

6. Evaluation Benchmark StructureThe 40-question benchmark evaluates four distinct inquiry types: Category 1 (16 queries): Direct course code lookups (e.g., "Prerequisites for CMSC 401")
   Category 2 (12 queries): Conversational titles without course numbers (e.g., "What do I need to take before Algorithm Analysis?")
   Category 3 (6 queries): Multi-hop rules and grade minimum constraints
   Category 4 (6 queries): Out-of-scope non-CMSC queries (e.g., Biology prerequisites, parking fees) to verify system abstention7.

AI DisclosureGenerative AI (google/flan-t5-base) was utilized strictly as an engineering component within the software pipeline to evaluate context-grounded answer generation and abstention behavior 
