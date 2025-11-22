# Question Creator Application (with LangChain)

[![Python](https://img.shields.io/badge/Python-3.12-blue?logo=python&logoColor=white)](https://www.python.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-%2300C9FF.svg?logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com/)
[![LangChain](https://img.shields.io/badge/LangChain-%23007ACC.svg?logo=langchain&logoColor=white)](https://python.langchain.com/)
[![OpenAI](https://img.shields.io/badge/OpenAI-%23000000.svg?logo=openai&logoColor=white)](https://openai.com/)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

Small, developer-friendly service that converts PDF documents into exam-style questions using LangChain and OpenAI models. The application exposes a small FastAPI site to upload PDFs, process them with LLM chains, and export generated Question/Answer pairs as CSV.

---

## Features

- Upload a PDF and persist it under `static/docs/`.
- Generate exam-style questions and answers from document content using LangChain LLM chains.
- Export Q/A pairs as `static/output/QA.csv` for download.
- Minimal, easy-to-run FastAPI app with `uvicorn`.

---

## Architecture

The project uses a simple, single-process service architecture. The main pieces are:

- Web layer: `FastAPI` serving a small UI (`templates/index.html`) and endpoints (`/upload`, `/analyze`).
- Worker/LLM layer: `src/helper.py` contains the LangChain pipeline that loads PDFs, creates embeddings, runs chains for question generation and answer synthesis, and returns results.
- Storage: uploaded PDFs are stored in `static/docs/` and generated outputs written to `static/output/QA.csv`.

Below is a high-level diagram (Mermaid):

```mermaid
flowchart LR
  Browser -->|POST /upload| FastAPI[FastAPI app (uvicorn)]
  FastAPI -->|saves file| Docs[static/docs/]
  FastAPI -->|POST /analyze| Helper[src/helper.py LLM pipeline]
  Helper -->|loads PDF| PDFLoader[PDF Loader (PyPDF / PyPDF2)]
  Helper -->|generates| LLM[OpenAI / LangChain Chains]
  LLM -->|writes CSV| Output[static/output/QA.csv]
  Browser -->|GET| Output
```

> Note: Some markdown renderers (including GitHub) don't render Mermaid diagrams by default. Use a previewer or the online Mermaid live editor to view it.

A standalone, editable Mermaid diagram is available at `diagrams/architecture.mmd`.

---

## Quick Start (Development)

Prerequisites

- Python 3.12 (tested)
- Conda or virtualenv (recommended)
- OpenAI API key configured in `.env` (if using the OpenAI models)

1. Create and activate the `llmapp` environment (recommended):

```powershell
conda create -n llmapp python=3.12 -y
conda activate llmapp
```

2. Install dependencies:

```powershell
pip install -r "d:\Research\Gen AI Project\Question-Creator-Application-with-Langchain\requirements.txt"
```

3. Create a `.env` file (if not present) and add your OpenAI key:

```
OPENAI_API_KEY=your_openai_api_key_here
```

4. Run the app locally:

```powershell
conda activate llmapp
uvicorn app:app --host 127.0.0.1 --port 8080 --reload
```

5. Open `http://127.0.0.1:8080/` in your browser to use the UI.

---

## API Endpoints

- `GET /` — Returns the UI (`templates/index.html`).
- `POST /upload` — Accepts a multipart/form-data file upload and `filename` form field. Saves the PDF to `static/docs/`.
- `POST /analyze` — Accepts form field `pdf_filename` (path returned from `/upload`) and returns JSON with `output_file` (CSV path). This endpoint triggers the LLM pipeline and writes `static/output/QA.csv`.

Example (curl-like) sequence:

```powershell
# Upload a PDF
curl -X POST "http://127.0.0.1:8080/upload" -F "pdf_file=@C:\path\to\document.pdf" -F "filename=document.pdf"

# Trigger analysis (use the returned pdf_filename from the upload response)
curl -X POST "http://127.0.0.1:8080/analyze" -F "pdf_filename=static/docs/document.pdf"
```

After analysis you can open `http://127.0.0.1:8080/static/output/QA.csv` to download results.

---

## Development notes & troubleshooting

- If you get import errors for PDF parsing, make sure `pypdf` (or `PyPDF2`) is installed. The code is compatible with `pypdf` and `PyPDF2` is also listed in `requirements.txt`.
- The project currently uses imports from `langchain` that produce deprecation warnings. Recommended actions:
  - Install `langchain-community` and update imports to `langchain_community.*` for document loaders, embeddings, and vectorstores.
  - Install `langchain-openai` and import `ChatOpenAI` / `OpenAIEmbeddings` from `langchain_openai` to adopt the latest split packages.

To upgrade and reduce deprecation warnings:

```powershell
pip install -U langchain-community langchain-openai
# Then update imports in `src/helper.py`:
#   - from langchain.document_loaders import PyPDFLoader
#     -> from langchain_community.document_loaders import PyPDFLoader
#   - from langchain.chat_models import ChatOpenAI
#     -> from langchain_openai import ChatOpenAI
#   - from langchain.embeddings.openai import OpenAIEmbeddings
#     -> from langchain_openai import OpenAIEmbeddings
```

- If the server binds to `0.0.0.0` you must use `http://127.0.0.1:8080/` or `http://localhost:8080/` in the browser. `0.0.0.0` is a bind address and is not routable in a browser.

---

## Project structure

```
.
├─ app.py                # FastAPI application and routes
├─ requirements.txt      # Python dependencies
├─ src/
|  |_prompt.py
│  └─ helper.py          # LangChain pipeline and PDF processing
├─ static/
│  ├─ docs/              # Uploaded PDFs
│  └─ output/            # Generated `QA.csv`
├─ templates/            # Jinja2 templates (index.html)
└─ README.md
```

---

## Contributing

Contributions are welcome. Please open an issue to discuss large changes first. For small fixes, create a PR with:

1. A meaningful title and description.
2. Tests (if applicable) or manual reproduction steps.
3. Keep changes focused and avoid unrelated refactors.

---

## License

This project is licensed under the MIT License — see the `LICENSE` file for details.

---

If you want, I can:

- Add a minimal `pyproject.toml`/`setup.py` to allow editable installs with `-e .`.
- Update `src/helper.py` to replace deprecated LangChain imports and the `Chain.run` calls with `invoke` usage.

Tell me which you'd like next and I will proceed.
# Question-Creator-Application-with-Langchain


### How to run?

1. Create an environment

```bash
conda create -n interview python=3.10 -y


conda activate interview

```

2. install requirements

```bash
pip install -r requirements.txt
```
