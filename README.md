# ⬡ RAG Studio — Enterprise Document Intelligence

[![FastAPI](https://img.shields.io/badge/FastAPI-0.111.0-009688.svg?style=flat-square&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com)
[![Python](https://img.shields.io/badge/Python-3.11+-3776AB.svg?style=flat-square&logo=python&logoColor=white)](https://www.python.org)
[![React](https://img.shields.io/badge/React-18.2-61DAFB.svg?style=flat-square&logo=react&logoColor=black)](https://react.dev)
[![Vite](https://img.shields.io/badge/Vite-5.4-646CFF.svg?style=flat-square&logo=vite&logoColor=white)](https://vitejs.dev)
[![License](https://img.shields.io/badge/License-MIT-blue.svg?style=flat-square)](LICENSE)
[![LLM Support](https://img.shields.io/badge/LLM-Groq%20%7C%20OpenAI%20%7C%20Anthropic-orange?style=flat-square)](https://groq.com)

**RAG Studio** is a production-grade, full-stack **Retrieval-Augmented Generation (RAG)** platform designed for conversational intelligence and semantic discovery across multi-format enterprise document collections.

Built with an asynchronous **FastAPI** backend and an **Anthropic Claude / Google Gemini** inspired **React** user interface, RAG Studio bridges vector similarity search, multi-provider LLM orchestration, and verifiable source citation grounding.

---

## 📑 Table of Contents

- [Key Capabilities](#-key-capabilities)
- [System Architecture](#-system-architecture)
- [Offline vs. Cloud Matrix](#-offline-vs-cloud-matrix)
- [Technology Stack](#-technology-stack)
- [Project Structure](#-project-structure)
- [Getting Started](#-getting-started)
  - [Prerequisites](#prerequisites)
  - [1. Backend Setup](#1-backend-setup)
  - [2. Frontend Setup](#2-frontend-setup)
- [Environment Configuration](#-environment-configuration)
- [API Reference](#-api-reference)
- [Deployment Guide](#-deployment-guide)
- [Roadmap & Enhancements](#-roadmap--enhancements)
- [License](#-license)

---

## ⚡ Key Capabilities

* **Multi-Format Ingestion**: Native extraction and parsing for `.pdf`, `.docx`, `.txt`, `.md`, `.csv`, and `.json`.
* **Sliding-Window Chunking**: Intelligent token-aware text segmentation with configurable overlap to preserve contextual continuity.
* **Vector Similarity & Grounding**: Vector retrieval with Cosine Similarity ranking and granular confidence match scoring ($0-100\%$).
* **Multi-Provider LLM Orchestration**:
  * **Groq** (`Llama-3-70B / 8B`) — Ultra-low latency inference.
  * **OpenAI** (`GPT-4o / GPT-3.5`) — High-accuracy general synthesis.
  * **Anthropic** (`Claude 3.5 Sonnet`) — Nuanced analytical reasoning.
  * **Context-Only Mode** — 100% offline fallback without external API dependencies.
* **Verifiable Source Grounding**: Interactive citation drawer showing exact file source, chunk index, similarity score, and excerpt previews.
* **Enterprise UX / UI**:
  * Dual-theme system (*Cosmic Obsidian Dark* & *Warm Minimalist Light*).
  * Collapsible sidebar with real-time vector statistics and quick document search filtering.
  * Floating frosted glass input dock with active knowledge scope detection.
  * Rich Markdown renderer with code blocks and one-click clipboard copying.

---

## 🏗️ System Architecture

```
                                  USER BROWSER
                         (React 18 + Vite + Custom CSS)
                                       │
                                [HTTP / REST]
                                       │
                                       ▼
                              FASTAPI BACKEND
                     ┌─────────────────────────────────┐
                     │   Async Router & Lifespan App   │
                     │  - Health & Diagnostics         │
                     │  - Document Indexing Queue      │
                     │  - Multi-turn Conversational RAG│
                     └────────────────┬────────────────┘
                                      │
            ┌─────────────────────────┴─────────────────────────┐
            ▼                                                   ▼
 ┌───────────────────────┐                           ┌────────────────────┐
 │  DOCUMENT PROCESSING  │                           │   VECTOR ENGINE    │
 ├───────────────────────┤                           ├────────────────────┤
 │ • PyPDF2 (PDF)        │                           │ • TF-IDF Matrix    │
 │ • python-docx (DOCX)  │                           │ • Cosine Distance  │
 │ • Text / CSV / JSON   │                           │ • Top-K Selection  │
 │ • Sliding Window Split│                           │ • Grounding Scorer │
 └──────────┬────────────┘                           └─────────┬──────────┘
            │                                                  │
            ▼                                                  ▼
 ┌───────────────────────┐                           ┌────────────────────┐
 │  METADATA STORE       │                           │  LLM INFERENCE     │
 ├───────────────────────┤                           ├────────────────────┤
 │ • SQLite (Database)   │                           │ • Groq API         │
 │ • File Storage (data/)│                           │ • OpenAI API       │
 │ • State & Chunk IDs   │                           │ • Anthropic API    │
 └───────────────────────┘                           │ • Offline Fallback │
                                                     └────────────────────┘
```

---

## 🔒 Offline vs. Cloud Matrix

RAG Studio is built on a **privacy-first, resilient design**. Core ingestion, chunking, and similarity retrieval operate 100% locally:

| Pipeline Step | Works Offline? | Requires API Key? | Compute Location |
| :--- | :---: | :---: | :--- |
| **Document Upload & Parsing** | ✅ Yes | ❌ No | Local Machine |
| **Text Chunking Engine** | ✅ Yes | ❌ No | Local Machine |
| **TF-IDF Vector Indexing** | ✅ Yes | ❌ No | Local Machine (scikit-learn) |
| **Semantic Similarity Search** | ✅ Yes | ❌ No | Local Machine (NumPy) |
| **Context-Only Grounded QA** | ✅ Yes | ❌ No | Local Machine |
| **AI LLM Answer Synthesis** | 🌐 Online | 🔑 Optional | Cloud (Groq / OpenAI / Claude) |

---

## 🛠️ Technology Stack

### Backend
* **Language**: Python 3.11+
* **Framework**: [FastAPI](https://fastapi.tiangolo.com/) + [Uvicorn](https://www.uvicorn.org/)
* **Vector Math & Embeddings**: [scikit-learn](https://scikit-learn.org/) (`TfidfVectorizer`), [NumPy](https://numpy.org/)
* **Document Parsers**: `PyPDF2`, `python-docx`
* **Database**: `SQLite3`
* **LLM Clients**: `groq`, `openai`, `anthropic`

### Frontend
* **Core Framework**: [React 18](https://react.dev/)
* **Build Tool**: [Vite 5](https://vitejs.dev/)
* **Design System**: Vanilla CSS3 (Custom design system inspired by Gemini & Claude)
* **Typography**: Google Fonts (*Outfit*, *Plus Jakarta Sans*, *Inter*, *JetBrains Mono*)

---

## 📂 Project Structure

```
.
├── backend/
│   ├── main.py                 # FastAPI application, routing & lifespan handler
│   ├── database.py             # SQLite schema, document & chunk CRUD operations
│   ├── document_processing.py  # Multi-format parsers & sliding-window chunker
│   ├── embeddings.py           # Vector space model & cosine similarity engine
│   └── search.py               # RAG prompt construction & LLM inference dispatch
├── src/                        # Frontend React Components
│   ├── App.jsx                 # Application orchestrator, theme & toast provider
│   ├── ChatArea.jsx            # Chat feed, hero starter cards & input dock
│   ├── ChatMessage.jsx         # Markdown renderer, code copy action & avatar
│   ├── DocumentItem.jsx        # Document card, file-type icons & chunk counter
│   ├── Sidebar.jsx             # Knowledge manager, drag & drop zone, search bar
│   ├── SourcePanel.jsx         # Grounding citations, relevance bars & excerpt viewer
│   ├── StatusBadge.jsx         # Pulse indicator badge system
│   ├── api.js                  # Asynchronous API client layer
│   ├── styles.css              # Enterprise Design System tokens & styles
│   └── main.jsx                # React DOM root mounting
├── data/                       # Local vector indices & uploaded artifacts (ignored)
├── index.html                  # HTML entry point with typography preconnects
├── package.json                # Frontend package configuration
├── requirements.txt            # Python dependencies
├── netlify.toml                # Static hosting & build configuration
└── .env.example                # Environment variables template
```

---

## 🚀 Getting Started

### Prerequisites
* **Python**: 3.11 or higher
* **Node.js**: 18.0 or higher
* **Package Managers**: `pip` and `npm`

---

### 1. Backend Setup

1. **Clone the repository**:
   ```bash
   git clone https://github.com/your-username/rag-studio.git
   cd rag-studio
   ```

2. **Create and activate a virtual environment**:
   ```bash
   # Windows (PowerShell)
   python -m venv .venv
   .venv\Scripts\Activate.ps1

   # Linux / macOS
   python3 -m venv .venv
   source .venv/bin/activate
   ```

3. **Install dependencies**:
   ```bash
   pip install -r requirements.txt
   ```

4. **Configure environment variables** *(Optional for LLM synthesis)*:
   ```bash
   cp .env.example .env
   ```
   Add your API keys in `.env`:
   ```env
   GROQ_API_KEY=gsk_your_free_groq_key_here
   OPENAI_API_KEY=sk_your_openai_key_here
   ```

5. **Start the API server**:
   ```bash
   uvicorn main:app --host 127.0.0.1 --port 8000 --reload
   ```
   * Interactive OpenAPI Documentation: [http://localhost:8000/docs](http://localhost:8000/docs)
   * System Health: [http://localhost:8000/health](http://localhost:8000/health)

---

### 2. Frontend Setup

1. **Install NPM dependencies**:
   ```bash
   npm install
   ```

2. **Launch the development server**:
   ```bash
   npm run dev
   ```

3. **Open the web application**:
   Navigate to [http://localhost:5173](http://localhost:5173) in your browser.

---

## 🔑 Environment Configuration

Create a `.env` file in the root directory:

```env
# ── LLM Providers (Priority: Groq -> OpenAI -> Anthropic -> Context-Only) ──

# Groq (Recommended for high-speed free inference: https://console.groq.com)
GROQ_API_KEY=gsk_...

# OpenAI (Optional)
OPENAI_API_KEY=sk-...

# Anthropic (Optional)
ANTHROPIC_API_KEY=sk-ant-...

# Frontend API Target (Optional — defaults to http://127.0.0.1:8000)
VITE_API_URL=http://127.0.0.1:8000
```

---

## 📡 API Reference

### Health & Diagnostics
* **`GET /health`**  
  Returns the active vector embedding model and LLM engine status.

### Document Management
* **`GET /documents`**  
  Returns a list of all indexed documents, chunk counts, and status flags.
* **`POST /upload-document`**  
  `multipart/form-data` payload. Uploads a file, extracts text, chunks it, and generates vector indices.
* **`GET /documents/{id}`**  
  Fetches status and metadata for a specific document.
* **`DELETE /documents/{id}`**  
  Removes document from SQLite and purges associated vector index entries.

### Semantic Search & Retrieval
* **`POST /search`**  
  Performs similarity search against indexed vector space.
  ```json
  {
    "query": "What are the key financial highlights?",
    "top_k": 5,
    "document_ids": [1, 2]
  }
  ```

### Conversational RAG
* **`POST /chat`**  
  Performs similarity search, injects grounded context into prompt template, and synthesizes answers using active LLM.
  ```json
  {
    "question": "Summarize the project timeline.",
    "top_k": 5,
    "document_ids": null,
    "conversation_history": [
      { "role": "user", "content": "What is the document about?" },
      { "role": "assistant", "content": "It outlines the Q3 engineering roadmap." }
    ]
  }
  ```

---

## 🌐 Deployment Guide

### Frontend Deployment (Netlify / Vercel)
The project includes a pre-configured [`netlify.toml`](./netlify.toml) file:
1. Connect your repository to **Netlify**.
2. **Build Command**: `npm run build`
3. **Publish Directory**: `dist`
4. Set `VITE_API_URL` under **Site Settings** → **Environment Variables** pointing to your deployed backend URL.

### Backend Deployment (Render / Railway / Docker)
1. Deploy as a Python Web Service.
2. **Start Command**: `uvicorn main:app --host 0.0.0.0 --port $PORT`
3. Configure `GROQ_API_KEY` or `OPENAI_API_KEY` in your provider's dashboard.

---

## 🗺️ Roadmap & Enhancements

- [x] Multi-format document chunking & vector search
- [x] Dual-theme Claude / Gemini inspired interface
- [x] Multi-turn conversation context management
- [x] Granular relevance percentage scores & citations
- [ ] Dense neural embeddings integration (`all-MiniLM-L6-v2` / `text-embedding-3-small`)
- [ ] Persistent user authentication & multi-workspace support
- [ ] Hybrid search (Dense Embeddings + BM25 Sparse Search)

---

## 📄 License

Distributed under the **MIT License**. See `LICENSE` for more information.
