# KSRCE Library RAG Chatbot

A secure, production-oriented **Retrieval-Augmented Generation (RAG) chatbot** for helping authenticated college students and staff search and understand approved library resources.

This README contains public project information only. Sensitive implementation details such as credentials, API keys, private infrastructure addresses, internal tokens, unrestricted document locations, and administrator information must never be committed to the repository.

## Abstract

The KSRCE Library RAG Chatbot uses artificial intelligence to answer questions from approved college library resources such as PDFs, scanned books, ebooks, images, and text documents. Instead of relying only on the language model’s pre-trained knowledge, the system retrieves relevant passages from the college collection and generates answers with source and page citations.

The system is designed for authenticated use by users with approved college email accounts. It focuses on low operating cost, reliable large-scale document ingestion, concurrent student access, source traceability, and protection against common AI and web security attacks.

## Main objectives

- Provide a login-protected library question-answering portal.

- Support PDF, scanned PDF, image, image-based PDF, EPUB, ebook, and text formats.

- Preserve source, chapter, section, and page information for citations.

- Use hybrid search combining keyword and semantic retrieval.

- Support multiple simultaneous users.

- Process a large book collection through resumable and auditable ingestion jobs.

- Minimize recurring AI cost through local embedding models and cost-controlled answer generation.

- Protect library content, user accounts, and internal system information.

## High-level features

- College email authentication and administrator-managed accounts

- Student, faculty, librarian, and system-operator roles

- Semantic, keyword, and hybrid library search

- Citation-based answers with source page references

- OCR for scanned pages and image-based documents

- Admin workflow for upload, review, publish, quarantine, and reprocessing

- Ingestion progress, error reporting, checksums, and retry support

- Conversation history and answer feedback

- Rate limiting, usage quotas, audit logging, and budget monitoring

- Protection against prompt injection, document poisoning, unauthorized retrieval, and unsafe output rendering

## Recommended technology direction

| Layer | Recommended technology |
| --- | --- |
| Web interface | Next.js, TypeScript, responsive UI |
| Backend API | Python FastAPI |
| Primary database | PostgreSQL with pgvector and PostgreSQL full-text search |
| Object storage | MinIO or another S3-compatible storage service |
| Queue/cache | Redis with a durable worker queue |
| Document processing | PyMuPDF, Apache Tika, EPUB/text parsers, OCRmyPDF/Tesseract |
| Embeddings | Self-hosted multilingual embedding model such as BGE-M3 or multilingual-e5-large |
| Answer model | Low-cost hosted Flash model or a college-hosted open-source model |
| Deployment | Docker-based deployment; scale to multiple services when usage requires it |

PostgreSQL with pgvector is recommended for the first production version because it combines relational metadata, access permissions, audit data, full-text search, and vector search in one manageable system. A dedicated vector database such as Qdrant can be introduced later if the collection or traffic becomes large enough to require independent scaling.

## Security principles

- Login is required before using chat, search, source preview, or conversation history.

- Passwords must be stored using a modern password-hashing algorithm such as Argon2id.

- API keys, passwords, tokens, private URLs, and server addresses must be stored outside Git.

- Library documents are treated as untrusted data and never as system instructions.

- Retrieval permissions are enforced on the server and database, not only in the user interface.

- Model output is validated and safely rendered; generated HTML, JavaScript, SQL, and shell commands are not executed.

- File uploads are type-checked, size-limited, virus-scanned, and processed in an isolated worker.

- New sources should be reviewed before becoming searchable.

- Rate limits, token limits, quotas, and budget alerts help prevent abuse and unexpected cost.

- Backups, restore tests, dependency scanning, and audit logs are required for production use.

## Approximate AI cost in Indian rupees

The following estimates are for planning and college permission only. Actual charges depend on the provider, model, currency conversion, taxes, rate limits, prompt size, output size, and usage pattern.

### Assumptions

- Exchange-rate planning assumption: **US$1 ≈ ₹85**.

- Average question: approximately 3,000 input tokens and 400 output tokens.

- 20% extra allowance for retries, safety checks, and variable prompt size.

- Local/self-hosted embeddings are used, so the full document collection does not incur recurring embedding API charges.

- The answer-generation estimate uses a low-cost Flash-class model as a planning baseline.

### Estimated monthly answer-generation cost

| Monthly questions | Approximate AI cost in USD | Approximate cost in INR |
| --- | --- | --- |
| 5,000 | $11–$12 | **₹950–₹1,050/month** |
| 20,000 | $45–$50 | **₹3,800–₹4,300/month** |
| 50,000 | $110–$125 | **₹9,350–₹10,625/month** |
| 100,000 | $220–$250 | **₹18,700–₹21,250/month** |

For the initial college proposal, a practical approval limit is:

> **₹10,000 per month for AI generation, supporting approximately 50,000 questions per month under the stated assumptions.**

The college should configure alerts at 50%, 80%, and 100% of the approved monthly budget.

### One-time embedding/indexing cost

| Approach | Approximate cost | Remarks |
| --- | --- | --- |
| Self-hosted embeddings | **₹0 API cost** | Requires server/GPU time, electricity, and maintenance. Recommended for the full collection. |
| Hosted text embeddings for a 300,000-page pilot | **Approximately ₹2,300–₹3,000** | Depends on average tokens per page, retries, provider pricing, and taxes. |
| Hosted text embeddings for a 1,000,000-page collection | **Approximately ₹7,500–₹10,000** | Re-embedding may be required if the model changes. |

These are model API estimates only. OCR, storage, server electricity, and engineering time are separate costs.

## Approximate infrastructure cost in India

The following ranges are broad planning estimates. Existing college servers can significantly reduce new expenditure.

### Option A: Use existing college infrastructure

| Item | Approximate cost |
| --- | --- |
| Application/API server allocation | ₹0–₹25,000 incremental cost |
| PostgreSQL and vector database server allocation | ₹0–₹50,000 incremental cost |
| Storage and backup allocation | ₹0–₹75,000 incremental cost |
| Optional GPU access | ₹0–₹1,50,000 incremental cost |
| Initial total | **Approximately ₹0–₹3,00,000**, depending on available resources |

### Option B: Purchase or provision dedicated pilot hardware

| Item | Approximate one-time cost |
| --- | --- |
| Application/API server | ₹60,000–₹1,50,000 |
| Database server with fast SSD/NVMe | ₹1,50,000–₹3,50,000 |
| Worker/OCR server | ₹75,000–₹2,00,000 |
| Optional 16–24 GB VRAM GPU server | ₹1,50,000–₹4,00,000 |
| 6 TB usable storage with backup capacity | ₹1,00,000–₹3,00,000 |
| Network, UPS, and monitoring allowance | ₹50,000–₹1,50,000 |
| Estimated initial infrastructure total | **₹5,85,000–₹15,50,000** |

A smaller pilot can begin with existing hardware and a limited corpus. A GPU is not mandatory if embeddings and answer generation use hosted services, but local GPU capability improves privacy and reduces long-term API dependence.

### Recommended first approval request

For a controlled production pilot, request:

- **AI operating budget:** ₹10,000/month, with usage alerts and a hard limit.

- **Storage:** 2 TB for a pilot, expandable to 6 TB for the initial production collection.

- **Servers:** application/API server, PostgreSQL server, ingestion/OCR worker, Redis, and backup target.

- **Optional GPU:** one 16–24 GB VRAM GPU for local embeddings, reranking, and future local model deployment.

- **Backup:** separate backup storage and regular restore testing.

## Data processing flow

```
Approved source
      ↓
File validation and checksum
      ↓
Text extraction or OCR
      ↓
Page and section preservation
      ↓
Structure-aware chunking
      ↓
Embedding generation
      ↓
Vector and keyword indexing
      ↓
Admin quality review
      ↓
Published library collection
      ↓
Authenticated question answering with citations
```

## Repository safety rules

Never commit the following:

- Passwords or password hashes

- API keys, access tokens, cookies, or private certificates

- Internal server IP addresses, private hostnames, or database URLs

- Unrestricted links to copyrighted books or private storage

- Student personal data or unredacted chat exports

- Production `.env` files

- Database dumps or uploaded library files

- Internal security reports or unpatched vulnerability details

Use `.env.example` with placeholder values and keep real configuration in a secret manager or protected deployment environment.

## Suggested development setup

1. Install the approved versions of Node.js, Python, PostgreSQL, Redis, and Docker.

1. Copy `.env.example` to a local environment file and fill only local development values.

1. Start local services with the project’s development compose configuration.

1. Run database migrations.

1. Create a local administrator account using a development-only command.

1. Import a small, legally approved sample collection.

1. Run ingestion and inspect page/chunk/error reports.

1. Create the evaluation question set before changing embedding or answer models.

1. Run unit, integration, security, retrieval, and load tests before deployment.

## Evaluation targets

Before opening the system to all students, the project should target:

- At least 90% relevant-document recall at top 10 on the approved test set.

- At least 90% citation precision for material answer claims.

- At least 95% refusal or “not found” behavior for out-of-corpus questions.

- No successful unauthorized cross-collection retrieval in security testing.

- No silent page or chunk loss during ingestion.

- Retrieval P95 latency below approximately 1.5 seconds under pilot load.

- Complete answer P95 latency below approximately 8 seconds for hosted generation.

- Successful backup restoration in a clean test environment.

## Project status

This repository contains the planning and product requirements for the KSRCE Library RAG Chatbot. Implementation should begin with a limited, approved pilot collection and expand only after quality, security, copyright, and operational reviews are complete.

## Team

- Abilash Kumar R

- Devaroopa E

## References

- [Google Gemini API pricing](https://ai.google.dev/gemini-api/docs/pricing)

- [Google Gemini embeddings documentation](https://ai.google.dev/gemini-api/docs/embeddings)

- [pgvector documentation](https://github.com/pgvector/pgvector)

- [Qdrant hybrid search documentation](https://qdrant.tech/documentation/search/hybrid-queries/)

- [OWASP Top 10 for LLM and GenAI applications](https://genai.owasp.org/llm-top-10/)