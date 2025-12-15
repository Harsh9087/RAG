## FastAPI + PostgreSQL + PGVector + OpenAI Embeddings – RAG Backend

This project is a Retrieval-Augmented Generation (RAG) backend built with **FastAPI**, **PostgreSQL + PGVector**, and **OpenAI embeddings**.  
It follows the same general design as the `litellm-pgvector` project by BerriAI, but exposes a simpler API suitable for university projects and demos [`github.com/BerriAI/litellm-pgvector`](https://github.com/BerriAI/litellm-pgvector).

### 1. Features

- **RAG pipeline**: Ingest documents, chunk, embed, store, and query.
- **PGVector similarity search**: Cosine similarity over `vector(1536)` embeddings.
- **Strict anti-hallucination prompts**: System + user prompts enforce “context-only” answers.
- **Admin endpoints**: Upload PDFs / DOCX / TXT for ingestion.
- **FastAPI**: Typed models, OpenAPI docs, CORS enabled.

---

### 2. Setup

#### 2.1. Install dependencies

```bash
pip install -r requirements.txt
```

#### 2.2. PostgreSQL + PGVector

Create a database (example: `rag_db`) and run:

```bash
psql -d rag_db -f schema.sql
```

Ensure `DATABASE_URL` in `.env` matches your database, e.g.:

```env
DATABASE_URL=postgresql://postgres:postgres@localhost:5432/rag_db
OPENAI_API_KEY=sk-...
EMBEDDING_MODEL=text-embedding-3-small
TOP_K=5
```

#### 2.3. Run the server

```bash
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

Open the docs at `http://localhost:8000/docs`.

---

### 3. API Overview

- **GET** `/health` – health check.
- **POST** `/ingest/text` – ingest raw text (admin).
- **POST** `/ingest/file` – ingest PDF / DOCX / TXT (admin).
- **POST** `/query` – run RAG query (user-facing).

You can plug the **System Prompt** and **User Prompt Template** from your assignment directly into `prompts.py`.

---

### 4. High-Level RAG Flow

- **Admin** uploads documents (file or text) → backend splits into chunks → generates embeddings (OpenAI) → stores chunks + embeddings in PostgreSQL `document_chunks` with PGVector column.
- **User** asks a question → query gets embedded → PGVector similarity search retrieves top-k chunks → chunks are placed into the user prompt template → LLM answers **only using that context**.

For a more complete OpenAI-compatible vector-store implementation, see the reference project: [`github.com/BerriAI/litellm-pgvector`](https://github.com/BerriAI/litellm-pgvector).


