# Vector DB POC with MuleSoft

A proof-of-concept demonstrating how to integrate a vector database into MuleSoft using **Postgres (pgvector extension)** for storage and **Ollama** for generating embeddings — enabling semantic/similarity search and RAG-style workflows through Mule flows.

## Overview

Most AI-native features — semantic search, RAG pipelines, AI agent "memory" — are fundamentally integration problems: data has to be extracted, embedded, stored, and retrieved. This POC explores building that pipeline using tools already familiar to MuleSoft developers, rather than adopting a dedicated vector database service.

## Architecture

Two flows make up this POC:

**1. Ingestion Pipeline**
Source data → Chunking → Ollama embeddings → Postgres (pgvector) storage

**2. Query / Retrieval Pipeline**
User query → Ollama embeddings → Similarity search (pgvector) → Mule retrieval flow → Consumer (agent/chatbot/API)

## Tech Stack

- **MuleSoft** (Mule 4.x / CloudHub or on-prem runtime)
- **PostgreSQL 18** with the **pgvector** extension
- **Ollama** (local) — embedding model: `nomic-embed-text`
- **MuleSoft Inference Connector**

## Prerequisites

- PostgreSQL 18 installed, with pgvector extension enabled (`CREATE EXTENSION vector;`)
- Ollama installed locally, running on `http://localhost:11434`, with the `nomic-embed-text` model pulled
- Anypoint Studio / Mule runtime set up
- MuleSoft Inference Connector installed

## Setup

1. Clone this repo
2. Enable pgvector in your Postgres database:
   ```sql
   CREATE EXTENSION IF NOT EXISTS vector;
   ```
3. Create the vector table (adjust dimension to match your embedding model's output):
   ```sql
   CREATE TABLE documents (
     id serial PRIMARY KEY,
     content text,
     embedding vector(768)
   );
   ```
4. Update the Mule project's database and Ollama connection properties to match your local environment
5. Deploy/run the Mule application

## Key Learnings

- Embedding models and chat/generation models are different — they need separate connector configs, not one config handling both
- The same embedding model must be used for both indexing and querying, or similarity search results become meaningless
- Local Postgres + pgvector setup has real first-time friction (extension availability varies by platform/version, TCP/IP and auth configuration)
- Ollama removes cloud dependency entirely for embeddings, making it ideal for local POC work — with the trade-off that it isn't a production-scale solution on its own

## Roadmap / Next Steps

- Benchmark similarity search performance at larger data volumes
- Evaluate dedicated vector databases (Pinecone, Milvus, Qdrant) for production-scale needs
- Extend the flow to include prompt construction + LLM generation for full RAG

## Author

Subhash Nagula — MuleSoft Integration Specialist
[LinkedIn](https://linkedin.com/in/subhash-nagula-b5a389242) · [GitHub](https://github.com/subhash-builds)
