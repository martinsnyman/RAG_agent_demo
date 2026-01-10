# Tax Agent Demo (RAG + Web Tools + Email)

This repository is a **demo** of a small “AI agent” workflow that can answer tax questions by combining:

- **PDF RAG**: chunks a local tax guide PDF, creates OpenAI embeddings, and stores them in a **local persistent Chroma** vector store.
- **LLM answering**: sends a single, consolidated prompt to an OpenAI model (default `gpt-5`) using the gathered context.
- **Optional tools**:
  - **Tavily** web search for external snippets.
  - **Playwright** page scraping (when a URL is provided/selected).
  - **Resend** email delivery of the final answer.
- **UI**: a local **Gradio** app with toggles for each tool.

The implementation lives in the notebook `agent_v001.ipynb`.

## What it can do

- Ingest a PDF and build a persistent vector store at `chroma/pdf_demo`.
- Retrieve relevant passages using MMR (diverse top-k) retrieval.
- If the question includes a page reference like “page 71”, perform **page-targeted retrieval** first.
- Optionally augment the prompt with:
  - Tavily search snippets
  - Playwright-scraped page text
- Optionally email the final answer via Resend.

## Requirements

- Windows + Python (the notebook is configured for a local venv kernel)
- API keys you provide:
  - `OPENAI_API_KEY`
  - `TAVILY_API_KEY` (only needed if you enable Tavily)
  - `RESEND_API_KEY` (only needed if you enable emailing)

## Setup

1) Create/activate a virtual environment (example):

```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
```

2) Install dependencies:

```powershell
pip install openai langchain langchain-openai langchain-community chromadb gradio python-dotenv requests pymupdf tiktoken numpy playwright
```

3) (Optional, only if you want scraping) Install Playwright browsers:

```powershell
playwright install
```

4) Set your environment variables in `.env` (this repo includes a starter file you can edit):

```env
OPENAI_API_KEY=your_key_here
TAVILY_API_KEY=your_key_here
RESEND_API_KEY=your_key_here
MODEL=gpt-5
```

## Configure the PDF

Open `agent_v001.ipynb` and update the `PDF_PATH` variable to point to a local PDF you want to use.

Notes:
- The vector store persists to `chroma/pdf_demo`. If you change PDFs and want a clean rebuild, delete that folder.

## Run

1) Start Jupyter:

```powershell
jupyter notebook
```

2) Open `agent_v001.ipynb` and run all cells.

3) A Gradio app launches locally (via `demo.launch(share=False)`). Open the printed local URL in your browser.

## Using the demo UI

- **Use PDF**: answers using retrieved PDF chunks (with page citations when available).
- **Use Tavily web search**: adds web snippets into the context.
- **Use Playwright to scrape a page**: scrapes either the first Tavily result URL or the URL you override.
- **Email Answer**: sends the final answer to the provided address via Resend.

## Notes / limitations

- This is a demo notebook (not hardened for production).
- Web search and scraping can introduce non-authoritative sources; validate important information.
- Large PDFs may take time on first run while embeddings and the Chroma index are created.
