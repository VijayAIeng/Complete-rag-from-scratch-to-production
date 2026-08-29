# Complete RAG From Scratch to Production

A practical exploration of Retrieval-Augmented Generation, starting with information retrieval and progressing toward advanced and production-ready RAG systems.

This repository is where I build and experiment with RAG systems end to end. It covers the complete lifecycle, from collecting and preparing documents to retrieval, reranking, generation, evaluation, and production monitoring.

The focus is on understanding how each part works, why different approaches are used, where they fail, and how to improve the system based on real experiments.

---

## RAG Architecture

The complete RAG pipeline can be divided into two parts: an offline knowledge pipeline and an online query pipeline.

### Complete Flow

```text
┌────────────┐
│ Documents  │
└─────┬──────┘
      │
      ▼
┌────────────┐
│ Ingestion  │
└─────┬──────┘
      │
      ▼
┌────────────┐
│  Parsing   │
└─────┬──────┘
      │
      ▼
┌────────────┐
│  Cleaning  │
└─────┬──────┘
      │
      ▼
┌────────────┐
│  Chunking  │
└─────┬──────┘
      │
      ▼
┌────────────┐
│  Metadata  │
└─────┬──────┘
      │
      ▼
┌────────────┐
│ Embeddings │
└─────┬──────┘
      │
      ▼
┌────────────┐
│  Indexing  │
└─────┬──────┘
      │
      ▼
┌────────────┐
│   Query    │
└─────┬──────┘
      │
      ▼
┌─────────────────┐
│ Query Processing│
└───────┬─────────┘
        │
        ▼
┌────────────┐
│ Retrieval  │
└─────┬──────┘
      │
      ▼
┌────────────┐
│ Filtering  │
└─────┬──────┘
      │
      ▼
┌────────────┐
│ Reranking  │
└─────┬──────┘
      │
      ▼
┌─────────────────────┐
│ Context Construction│
└──────────┬──────────┘
           │
           ▼
┌────────────┐
│ Generation │
└─────┬──────┘
      │
      ▼
┌────────────┐
│ Validation │
└─────┬──────┘
      │
      ▼
┌────────────────────┐
│ Citation / Grounding│
└──────────┬─────────┘
           │
           ▼
┌────────────┐
│ Evaluation │
└─────┬──────┘
      │
      ▼
┌────────────┐
│ Monitoring │
└─────┬──────┘
      │
      ▼
┌────────────┐
│  Feedback  │
└────────────┘
```

For a high-level view, the same architecture can be represented as:

```text
Documents
    │
    ▼
Ingestion → Parsing → Cleaning → Chunking → Metadata → Embeddings → Index
                                                                    │
                                                                    ▼
User Query → Query Processing → Retrieval → Filtering → Reranking → Context
                                                                    │
                                                                    ▼
                                                            Generation
                                                                    │
                                                                    ▼
                                                     Validation → Grounding
                                                                    │
                                                                    ▼
                                                   Evaluation → Monitoring
                                                                    │
                                                                    ▼
                                                               Feedback
```

---

## What I Am Exploring

### 1. Document Processing

Before retrieval can work, the source data needs to be understood and prepared correctly.

I explore:

* PDF documents
* HTML pages
* Markdown
* Text files
* DOCX
* CSV and JSON
* Databases
* Web content
* Code repositories

The goal is to understand how raw documents become usable knowledge for a retrieval system.

---

### 2. Parsing and Cleaning

Document extraction is often more difficult than it looks.

I explore problems such as:

* Broken PDF extraction
* Headers and footers
* Tables
* Duplicate content
* HTML navigation
* Formatting artifacts
* Encoding issues
* Repeated sections
* Missing text

The important question is not simply "How do I clean the data?"

It is:

> What information can I safely remove without damaging retrieval quality?

---

### 3. Chunking

Chunking determines how documents are divided before they are embedded and indexed.

I explore:

* Fixed-size chunking
* Token-based chunking
* Sentence chunking
* Paragraph chunking
* Recursive chunking
* Structure-aware chunking
* Semantic chunking
* Parent-child chunking
* Hierarchical chunking

Experiments will compare how different chunking strategies affect retrieval quality.

---

### 4. Metadata

Metadata provides additional information about each chunk.

Example:

```json
{
  "document_id": "employee_handbook",
  "page": 42,
  "section": "Leave Policy",
  "source": "employee_handbook.pdf"
}
```

Metadata can later be used for:

* Filtering
* Permissions
* Source attribution
* Debugging
* Evaluation
* Multi-tenant retrieval

---

### 5. Embeddings

Text needs to be represented in a form that allows semantic comparison.

I explore:

* Sentence embeddings
* Document embeddings
* Dense representations
* Embedding dimensions
* Similarity metrics
* Cosine similarity
* Dot product
* Multilingual embeddings
* Domain-specific embeddings

I will also compare different embedding models rather than treating embeddings as a black box.

---

### 6. Indexing

Once embeddings are created, they need to be indexed for efficient search.

Topics include:

* Exact nearest-neighbor search
* Approximate nearest-neighbor search
* FAISS
* HNSW
* IVF
* Product quantization
* Vector databases

The goal is to understand the tradeoff between search quality, memory, latency, and scale.

---

## Retrieval

Retrieval is the core of a RAG system.

I explore multiple retrieval strategies rather than relying only on vector search.

### Sparse Retrieval

* TF-IDF
* BM25
* Inverted indexes
* Keyword matching

### Dense Retrieval

* Embedding search
* Vector similarity
* Semantic retrieval

### Hybrid Retrieval

Combining sparse and dense retrieval when both lexical and semantic signals are useful.

```text
                 Query
                   │
          ┌────────┴────────┐
          ▼                 ▼
     Sparse Search     Dense Search
          │                 │
          └────────┬────────┘
                   ▼
                Fusion
                   │
                   ▼
               Reranking
```

---

## Query Processing

The original user query is not always the best query for retrieval.

I explore:

* Query rewriting
* Query expansion
* Query normalization
* Query classification
* Multi-query retrieval
* Query decomposition
* HyDE
* Sub-question generation

The purpose is to improve the quality of the information retrieved for the user's actual intent.

---

## Reranking

Initial retrieval usually produces a larger candidate set.

A reranker can then determine which candidates are actually the most relevant.

```text
Query
  │
  ▼
Retriever
  │
  ▼
Top-K Candidates
  │
  ▼
Reranker
  │
  ▼
Relevant Context
```

I explore:

* Cross-encoder reranking
* Bi-encoder retrieval
* Late-interaction approaches
* Relevance scoring
* Top-K selection

---

## Context Construction

Retrieving documents is not the final step.

The retrieved information needs to be organized before it is sent to the language model.

I explore:

* Context ordering
* Duplicate removal
* Context compression
* Relevance thresholds
* Token budgets
* Source grouping
* Parent context
* Citation mapping
* Lost-in-the-middle considerations

A good retriever can still produce a poor answer if the context is constructed badly.

---

## Generation

The language model receives the selected context and generates the final response.

I explore:

* Prompt construction
* System instructions
* Context formatting
* Citation generation
* Structured outputs
* Streaming
* Temperature
* Top-K
* Top-P
* Stop conditions

The focus is on understanding how retrieval and generation interact rather than treating the LLM as an isolated component.

---

# Types of RAG

The repository covers the evolution of RAG architectures.

```text
Basic RAG
    │
    ├── Dense RAG
    ├── Sparse RAG
    └── Hybrid RAG
            │
            ▼
       Reranking RAG
            │
            ▼
       Advanced RAG
            │
      ┌─────┼───────────┐
      │     │           │
      ▼     ▼           ▼
 Query   Hierarchical  Parent-Child
 RAG        RAG          RAG
      │
      ▼
  Corrective RAG
      │
      ▼
    Self-RAG
      │
      ▼
  Adaptive RAG
      │
      ├── Graph RAG
      ├── Multimodal RAG
      ├── Structured Data RAG
      ├── Code RAG
      └── Agentic RAG
              │
              ▼
        Production RAG
```

---

# RAG Approaches

## Basic RAG

```text
Query → Retrieve → Context → Generate
```

The starting point for understanding the RAG concept.

## Hybrid RAG

```text
Query → Sparse + Dense Retrieval → Fusion → Reranking → Generate
```

Combines lexical and semantic retrieval.

## Advanced RAG

Introduces improvements around:

* Better chunking
* Better embeddings
* Query transformation
* Reranking
* Context compression
* Metadata filtering

## Corrective RAG

The system checks whether retrieved information is useful and takes corrective action when retrieval quality is poor.

## Self-RAG

The model can determine when retrieval is necessary and evaluate the retrieved information during generation.

## Adaptive RAG

The system chooses different retrieval strategies depending on the query.

## Graph RAG

Uses graph structures to retrieve information based on entities and relationships.

## Multimodal RAG

Extends retrieval beyond text to sources such as:

* Images
* Tables
* Audio
* Video
* Documents

## Structured Data RAG

Works with structured sources such as:

* SQL databases
* Tables
* Business systems
* Analytics data

## Code RAG

Retrieves relevant information from software repositories.

It can work with:

* Functions
* Classes
* Modules
* Documentation
* Dependencies
* Code relationships

## Agentic RAG

RAG becomes one capability inside an agentic system.

```text
                         Agent
                           │
          ┌────────────────┼────────────────┐
          ▼                ▼                ▼
       Retrieval          Tools          Search
          │                │                │
          └────────────────┼────────────────┘
                           ▼
                        Reasoning
                           │
                           ▼
                       Validation
                           │
                           ▼
                         Answer
```

---

# RAG Evaluation

A RAG system should not be evaluated only by looking at the final answer.

I evaluate different stages independently.

### Retrieval

* Recall@K
* Precision@K
* Hit Rate
* MRR
* NDCG

### Context

* Context relevance
* Context completeness
* Context precision
* Duplicate information

### Generation

* Faithfulness
* Groundedness
* Relevance
* Factuality
* Completeness

### System

* Latency
* Throughput
* Token usage
* Memory
* Cost

---

# RAG Failure Analysis

A major part of this repository is understanding why RAG systems fail.

Examples:

```text
Bad Source Data
      ↓
Poor Parsing
      ↓
Poor Chunking
      ↓
Weak Embeddings
      ↓
Poor Retrieval
      ↓
Incorrect Ranking
      ↓
Bad Context
      ↓
Hallucinated Answer
```

I will investigate failures at the individual component level rather than simply changing the LLM.

---

# Production RAG

The final stage is turning the experimental pipeline into a production system.

```text
                        Client
                          │
                          ▼
                     API Gateway
                          │
                          ▼
                   Query Service
                          │
                          ▼
                    RAG Pipeline
                          │
             ┌────────────┼────────────┐
             ▼            ▼            ▼
         Retriever     Reranker       LLM
             │            │            │
             └────────────┼────────────┘
                          ▼
                    Validation
                          │
                          ▼
                       Response
                          │
             ┌────────────┴────────────┐
             ▼                         ▼
        Observability              Evaluation
```

Production topics include:

* API design
* Model serving
* Vector database design
* Caching
* Batch processing
* Streaming
* GPU inference
* Authentication
* Authorization
* Multi-tenancy
* Data isolation
* Logging
* Metrics
* Tracing
* Monitoring
* Scaling
* Cost optimization
* Failure handling

---

# Security

RAG systems can expose sensitive information if retrieval and access control are not designed correctly.

I explore:

* Prompt injection
* Indirect prompt injection
* Data poisoning
* Unauthorized document retrieval
* Access control
* Tenant isolation
* Sensitive information leakage
* Document-level permissions
* Retrieval filtering

---

# Performance

Production RAG requires more than answer quality.

I measure:

```text
Embedding Latency
Retrieval Latency
Reranking Latency
Time To First Token
Generation Latency
End-to-End Latency
Throughput
Memory Usage
Token Usage
Storage
Cost Per Request
```

The goal is to understand the relationship between:

```text
Quality
Latency
Memory
Scale
Cost
```

---

# Experiments

Each major technique will be explored through experiments.

A typical experiment will contain:

```text
Objective
Dataset
Configuration
Implementation
Evaluation
Results
Observations
Limitations
Next Experiment
```

This makes it possible to compare different RAG approaches using the same data and evaluation methodology.

---

# Repository Structure

```text
complete-rag-from-scratch-to-production/
│
├── README.md
│
├── 01_information_retrieval/
├── 02_document_ingestion/
├── 03_document_parsing/
├── 04_document_cleaning/
├── 05_chunking/
├── 06_metadata/
├── 07_embeddings/
├── 08_indexing/
├── 09_sparse_retrieval/
├── 10_dense_retrieval/
├── 11_hybrid_retrieval/
├── 12_reranking/
├── 13_query_processing/
├── 14_query_rewriting/
├── 15_context_construction/
├── 16_basic_rag/
├── 17_advanced_rag/
├── 18_corrective_rag/
├── 19_self_rag/
├── 20_adaptive_rag/
├── 21_graph_rag/
├── 22_multimodal_rag/
├── 23_structured_data_rag/
├── 24_code_rag/
├── 25_agentic_rag/
├── 26_rag_evaluation/
├── 27_rag_optimization/
├── 28_rag_security/
├── 29_rag_inference/
├── 30_rag_serving/
└── 31_production_rag/
│
├── data/
│   ├── raw/
│   ├── interim/
│   └── processed/
│
├── src/
│   └── rag/
│       ├── ingestion/
│       ├── parsing/
│       ├── chunking/
│       ├── embeddings/
│       ├── indexing/
│       ├── retrieval/
│       ├── reranking/
│       ├── query/
│       ├── context/
│       ├── generation/
│       ├── evaluation/
│       └── monitoring/
│
├── experiments/
├── notebooks/
├── configs/
├── scripts/
├── tests/
├── artifacts/
└── docker/
```

---

# Technology Areas

### Machine Learning

* Python
* NumPy
* Pandas
* Scikit-learn

### Deep Learning

* PyTorch
* Transformers

### Embeddings

* Sentence Transformers
* Modern embedding models
* Multilingual embedding models

### Retrieval

* BM25
* FAISS
* HNSW
* Vector databases
* Hybrid search

### LLMs

* Hugging Face
* Open-source LLMs
* API-based LLMs

### Application

* FastAPI
* Pydantic

### Infrastructure

* Docker
* Kubernetes
* Cloud platforms

### Observability

* Logging
* Metrics
* Tracing
* Experiment tracking

---

# Final Objective

The goal of this repository is to understand RAG as a complete system.

Not just:

```text
Vector Database + LLM
```

But:

```text
Data
    ↓
Document Processing
    ↓
Chunking
    ↓
Representation
    ↓
Indexing
    ↓
Retrieval
    ↓
Ranking
    ↓
Context Construction
    ↓
Generation
    ↓
Grounding
    ↓
Evaluation
    ↓
Monitoring
    ↓
Continuous Improvement
```

The repository will evolve from simple experiments into a production-oriented RAG platform while documenting the decisions, experiments, failures, and improvements along the way.
