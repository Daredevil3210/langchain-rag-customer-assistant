**English** | [简体中文](README.zh.md)

# LangChain RAG Customer Assistant

A focused customer-support knowledge-base exercise built with LangChain and Milvus. A single Jupyter notebook turns the local `knowledge.txt` into searchable vectors, retrieves relevant passages with SiliconFlow embeddings, and passes the question and context to a chat agent.

This repository organizes a user-provided Atguigu LangChain tutorial example. It contains the notebook, knowledge text, original full dependency list, and setup notes. Product names, plans, and refund policies in the corpus are demonstration material, not real service commitments. This is a learning notebook, not a deployed support service.

## What the notebook demonstrates

```text
knowledge.txt → load and split → embed → Milvus
user question → embed and search → retrieved context → chat agent answer
```

The agent has no tools; retrieval happens explicitly in `generate_answer(query)` before the question and context are sent to it. The notebook prints retrieved chunks, source metadata, similarity scores, and the final answer.

## Repository contents

- [Customer-support notebook](chapter10-RAG/05-案例：Atguigu%20Assistant客服知识库.ipynb): configuration, indexing, retrieval, and an example question.
- [knowledge.txt](knowledge.txt): the Chinese demonstration corpus.
- [requirements_full.txt](requirements_full.txt): dependency pins retained from the wider tutorial.
- [.env.example](.env.example): placeholder API configuration.
- [v0.1.0 notes](releases/v0.1.0.md): original cleanup and validation record.
- [LICENSE](LICENSE): license for the added organization/documentation work, subject to the source-content attribution below.

## Prepare the environment

The original example uses Python 3.13. Create and activate an isolated environment, then install dependencies from the repository root:

```bash
git clone https://github.com/Daredevil3210/langchain-rag-customer-assistant.git
cd langchain-rag-customer-assistant
python -m venv .venv
# Activate: source .venv/bin/activate (macOS/Linux)
# PowerShell: .\.venv\Scripts\Activate.ps1
python -m pip install -r requirements_full.txt
```

The full manifest includes components for other tutorial chapters and is not a minimal dependency set for this notebook. The original release notes state that the complete environment was not reinstalled for compatibility verification.

Start a Milvus server supporting database operations. The default URI is `http://localhost:19530`; update `MILVUS_URI` in the notebook if needed. Installing pymilvus only installs the client, not the server.

Copy `.env.example` to a local `.env` if no configuration exists. Use `cp .env.example .env` on macOS/Linux or `Copy-Item .env.example .env` in PowerShell. Fill in your own configuration:

```dotenv
SILICONFLOW_API_KEY=your_siliconflow_api_key
SILICONFLOW_BASE_URL=your_siliconflow_openai_compatible_base_url
CLOSEAI_API_KEY=your_closeai_api_key
CLOSEAI_BASE_URL=your_closeai_openai_compatible_base_url
```

SiliconFlow supplies `Pro/BAAI/bge-m3` embeddings. The chat model is initialized with `init_chat_model()` through the CLOSEAI OpenAI-compatible endpoint, with `gpt-5.4-mini` as the example model name. Use identifiers supported by your provider account. Hosted calls need network access and may incur charges. Keep credentials in the ignored local environment file.

## Run the example

Start Jupyter from the notebook directory so `../knowledge.txt` resolves:

```bash
cd chapter10-RAG
jupyter notebook
```

Open the customer-support notebook, select your environment as its kernel, and run cells in order:

1. Review the service URI, database/collection names, and knowledge-file path.
2. Create the database and initialize a collection with 1024-dimensional COSINE vectors.
3. Initialize embeddings, load the corpus, and split it with chunk size 200 and overlap 80.
4. Generate vectors, upsert records with text/source/chunk IDs, then flush.
5. Initialize the chat agent, embed a question, and retrieve relevant chunks.
6. Send the question and context to the agent and inspect its answer.

**Collection replacement:** initialization deletes and recreates the named collection. Defaults are database `rag_tutorial` and collection `docs`. Use a dedicated practice database or change the names before running if existing data must be preserved.

The example asks about a seven-day refund rejection. `generate_answer()` retrieves at most five passages; the standalone `retrieve()` helper defaults to three. Embedding dimensions must match the collection if you change models.

## Existing cleanup and validation record

The [v0.1.0 notes](releases/v0.1.0.md) record these earlier changes:

- Corrected `generate_answer()` to pass its actual `query` to `retrieve()`.
- Cleared saved outputs and execution metadata while preserving notebook attachments.
- Converted the dependency file from UTF-16 to UTF-8 without changing versions or comments.
- Checked notebook format, code syntax, local asset references, and common sensitive-data patterns.
- Used simulated embeddings, Milvus, and an agent to check question, retrieval, and prompt flow.

That earlier validation did not connect to real Milvus or call online models end to end. No repository scripts, dependency installation, or online services were executed for this documentation refresh. The repository does not include a standalone automated test suite.

## Attribution and license

This example was organized from a user-provided Atguigu LangChain tutorial case. Added organization and documentation are licensed under [MIT](LICENSE). Rights to the original tutorial code and knowledge text remain with their respective owners; third-party use and redistribution remain subject to their original permissions.
