---
publish: true
---

# NL→SQL Approaches: State of the Field (2025)
[[client-wilsch-group]]

**Purpose:** Reference document for understanding AI-powered SQL generation approaches and tools.

---

## Approach Categories

### 1. Direct LLM-based SQL Generation

Prompt LLM with schema → LLM outputs SQL directly.

- Simple, fast, no infrastructure
- Struggles with complex schemas, hallucinates columns, no error recovery

### 2. RAG-Enhanced Text2SQL

Store schema docs, example queries, business glossary in vector DB. Retrieve relevant context → feed to LLM → generate SQL.

- Context-aware, handles business terminology
- Requires setup (embeddings, vector DB), retrieval quality matters

### 3. Multi-Agent / Iterative Systems

Multiple agents with different roles: Schema Linker → Query Planner → SQL Generator → Validator. Generate → Execute → Check → Fix loop.

- Best accuracy, self-correcting
- More complex, slower, higher cost

### 4. Semantic Layer Approach

Pre-define business logic in modeling language. LLM queries semantic layer, not raw tables.

- Business logic encoded once, consistent results
- Upfront modeling work, less flexible for ad-hoc queries

---

## Tool Landscape

| Tool | Approach | Setup Time | Data Privacy | License |
|------|----------|------------|--------------|---------|
| **MindsDB** | Federated + MCP | Days-Weeks | Excellent | Open source |
| **Wren AI** | Semantic Layer | ~3 min | Metadata-only | Open source |
| **Vanna AI** | RAG + Self-learning | Hours | Local deployment | MIT |
| **IBM watsonx** | Enterprise Lakehouse | Weeks-Months | Enterprise-grade | Commercial |

### MindsDB

Federated Query Engine with Model Context Protocol (MCP). Connects 200+ data sources without data movement. Agent-to-Agent communication between specialized agents. Knowledge Bases provide RAG capability.

### Wren AI

Semantic-first architecture with Modeling Definition Language (MDL). Built on Apache DataFusion (Rust). Metadata-only approach - data never sent to LLM. Supports dbt integration and Slack embedding.

### Vanna AI

RAG framework with self-learning. Stores question-SQL pairs in vector DB, improves with corrections. Function RAG transforms pairs into invokable tools. Database contents never sent to LLM by default.

### IBM watsonx.data Text2SQL

Enterprise lakehouse with autonomous agents. Multi-engine optimization (Presto, Spark, Milvus). Built-in governance, lineage, audit trails. 40% more accurate for unstructured data vs conventional RAG.

---

## Key Industry Insight

Modern production systems combine multiple techniques: LLM understanding + Semantic Layer + RAG retrieval + Multi-Agent iteration + Symbolic validation.

Pure LLM approaches achieve ~86% on academic benchmarks (Spider 1.0) but only ~6% on enterprise benchmarks (Spider 2.0 with 1000+ column schemas).

---

## Relevant Research

| Paper | Contribution |
|-------|--------------|
| Spider 2.0 (2024) | Enterprise benchmark exposing real-world accuracy gap |
| TableRAG (2024) | Million-token table understanding via schema+cell retrieval |
| SQLGenie - Amazon (2025) | Multi-agent achieving 92.8% WikiSQL |
| SQL-of-Thought (2024) | Taxonomy-guided error correction |
| RAISE (2025) | End-to-end reasoning agent with refinement |

---

## Sources

- [State of Text2SQL 2024](https://blog.premai.io/state-of-text2sql-2024/)
- [MindsDB](https://mindsdb.com/) | [GitHub](https://github.com/mindsdb/mindsdb)
- [Wren AI](https://www.getwren.ai/oss) | [GitHub](https://github.com/Canner/WrenAI)
- [Vanna AI](https://vanna.ai/) | [GitHub](https://github.com/vanna-ai/vanna)
- [Spider 2.0](https://spider2-sql.github.io/)
- [TableRAG](https://arxiv.org/html/2506.10380v1)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

