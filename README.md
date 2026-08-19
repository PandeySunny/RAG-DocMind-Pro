# ⬡ RAG Studio — Enterprise Document Intelligence & Private Knowledge Assistant

[![FastAPI](https://img.shields.io/badge/FastAPI-0.111.0-009688.svg?style=flat-square&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com)
[![Python](https://img.shields.io/badge/Python-3.11+-3776AB.svg?style=flat-square&logo=python&logoColor=white)](https://www.python.org)
[![React](https://img.shields.io/badge/React-18.2-61DAFB.svg?style=flat-square&logo=react&logoColor=black)](https://react.dev)
[![Vite](https://img.shields.io/badge/Vite-5.4-646CFF.svg?style=flat-square&logo=vite&logoColor=white)](https://vitejs.dev)
[![Privacy First](https://img.shields.io/badge/Security-Session--Isolated%20%7C%20Zero--Retention-green.svg?style=flat-square)](https://github.com)
[![LLM Support](https://img.shields.io/badge/LLM-Groq%20%7C%20OpenAI%20%7C%20Anthropic-orange?style=flat-square)](https://groq.com)
[![License](https://img.shields.io/badge/License-MIT-blue.svg?style=flat-square)](LICENSE)

**RAG Studio** is a production-ready, full-stack **Retrieval-Augmented Generation (RAG)** platform designed for conversational intelligence, vector-grounded question answering, and semantic search across private document repositories.

Engineered with an asynchronous **FastAPI** backend and an **Anthropic Claude & Google Gemini** inspired **React** web interface, RAG Studio provides multi-tenant session isolation, verifiable source citation grounding, and an ephemeral zero-retention lifecycle.

---

## 📑 Table of Contents

- [Key Capabilities](#-key-capabilities)
- [Multi-Tenant Privacy & Auto-Purge Lifecycle](#-multi-tenant-privacy--auto-purge-lifecycle)
- [System Architecture](#-system-architecture)
- [Offline vs. Cloud Matrix](#-offline-vs-cloud-matrix)
- [Technology Stack](#-technology-stack)
- [Project Directory Layout](#-project-directory-layout)
- [Getting Started](#-getting-started)
  - [Prerequisites](#prerequisites)
  - [1. Backend Setup](#1-backend-setup)
  - [2. Frontend Setup](#2-frontend-setup)
- [Environment Variables](#-environment-variables)
- [API Reference](#-api-reference)
- [Deployment Guide](#-deployment-guide)
- [License](#-license)

---

## ⚡ Key Capabilities

* **Multi-Tenant Session Isolation**: Cryptographic client session IDs (`X-Session-ID`) ensure complete data segregation. User A can never see, access, or query User B's documents.
* **Ephemeral Zero-Retention Lifecycle**:
  * **On-Demand Wipe**: One-click **"Purge & Reset Session"** immediately deletes all uploaded files, SQLite chunks, and vector embeddings.
  * **Auto-TTL Sweeper**: Background thread automatically cleans up inactive sessions and orphaned files older than 2 hours.
  * **Server Shutdown Purge**: When the backend server is stopped or restarted, all uploaded temporary files and memory vectors are permanently destroyed.
* **Multi-Format Ingestion**: Native extraction and parsing for `.pdf`, `.docx`, `.txt`, `.md`, `.csv`, and `.json`.
* **Sliding-Window Chunking**: Token-aware text segmentation with configurable overlap to preserve contextual continuity.
* **Vector Similarity & Grounding**: Vector retrieval with Cosine Similarity ranking and granular confidence match scoring ($0-100\%$).
* **Multi-Provider LLM Orchestration**:
  * **Groq** (`Llama-3-70B / 8B`) — Ultra-low latency inference.
  * **OpenAI** (`GPT-4o / GPT-3.5`) — High-accuracy general synthesis.
  * **Anthropic** (`Claude 3.5 Sonnet`) — Nuanced analytical reasoning.
  * **Context-Only Mode** — 100% offline fallback without external API dependencies.
* **Verifiable Source Grounding**: Interactive citation drawer showing exact file source, chunk index, similarity score bar, and excerpt previews.
* **Enterprise UX / UI**:
  * Dual-theme system (*Cosmic Obsidian Dark* & *Warm Minimalist Light*).
  * Collapsible sidebar with real-time vector statistics and quick document search filtering.
  * Floating frosted glass input dock with active knowledge scope detection.
  * Rich Markdown renderer with code blocks and one-click clipboard copying.

---

## 🛡️ Multi-Tenant Privacy & Auto-Purge Lifecycle

```
[Browser Client A] ──(X-Session-ID: sess_abc)──┐
                                               ├──► [FastAPI Backend]
[Browser Client B] ──(X-Session-ID: sess_xyz)──┘         │
                                                         ▼
                                               [Session-Scoped DB & Vector Space]
                                               ├─ Session ABC Docs & Embeddings (Private)
                                               └─ Session XYZ Docs & Embeddings (Private)
                                                         │
                                  ┌──────────────────────┴──────────────────────┐
                                  ▼                                             ▼
                     [Manual "Purge Session"]                      [Auto-TTL / Server Shutdown]
                     Immediate destruction of files,              Background daemon sweeps stale
                     database rows, and embeddings.               sessions >2h & wipes files on exit.
```

1. **Client Generation**: The browser creates a cryptographically unique `session_id` (`crypto.randomUUID()`) stored in `sessionStorage`.
2. **Strict Scoping**: SQLite queries and TF-IDF vector matrices partition indices strictly by `session_id`.
3. **Guaranteed Ephemerality**:
   * Users can wipe their session data on demand with **"Purge & Reset Session"**.
   * Server shutdown hook cleans up all uploaded files from `data/uploads/`.
   * Background TTL cleaner purges idle sessions after 2 hours.

---

## 🏗️ System Architecture

```
                                  USER BROWSER
                         (React 18 + Vite + Custom CSS)
                                       │
                                [HTTP / REST]
                                (X-Session-ID)
                                       │
                                       ▼
                              FASTAPI BACKEND
                     ┌─────────────────────────────────┐
                     │   Async Router & Lifespan App   │
                     │  - Health & Diagnostics         │
                     │  - Session Document Ingestion   │
                     │  - Multi-turn Conversational RAG│
                     │  - Ephemeral TTL Worker (Daemon)│
                     └────────────────┬────────────────┘
                                      │
            ┌─────────────────────────┴─────────────────────────┐
            ▼                                                   ▼
 ┌───────────────────────┐                           ┌────────────────────┐
 │  DOCUMENT PROCESSING  │                           │   VECTOR ENGINE    │
 ├───────────────────────┤                           ├────────────────────┤
 │ • PyPDF2 (PDF)        │                           │ • TF-IDF Matrix    │
 │ • python-docx (DOCX)  │                           │ • Cosine Distance  │
 │ • Text / CSV / JSON   │                           │ • Session Filter   │
 │ • Sliding Window Split│                           │ • Grounding Scorer │
 └──────────┬────────────┘                           └─────────┬──────────┘
            │                                                  │
            ▼                                                  ▼
 ┌───────────────────────┐                           ┌────────────────────┐
 │  METADATA STORE       │                           │  LLM INFERENCE     │
 ├───────────────────────┤                           ├────────────────────┤
 │ • SQLite (Database)   │                           │ • Groq API         │
 │ • File Storage (data/)│                           │ • OpenAI API       │
 │ • Session Indexes     │                           │ • Anthropic API    │
 └───────────────────────┘                           │ • Offline Fallback │
                                                     └────────────────────┘
```

---

## 🔒 Offline vs. Cloud Matrix

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
* **Database**: `SQLite3` (Session-Indexed)
* **LLM Clients**: `groq`, `openai`, `anthropic`

### Frontend
* **Core Framework**: [React 18](https://react.dev/)
* **Build Tool**: [Vite 5](https://vitejs.dev/)
* **Design System**: Vanilla CSS3 (Custom design system inspired by Gemini & Claude)
* **Typography**: Google Fonts (*Outfit*, *Plus Jakarta Sans*, *Inter*, *JetBrains Mono*)

---

## 📂 Project Directory Layout

```
.
├── backend/
│   ├── main.py                 # FastAPI application, session routing & shutdown cleaner
│   ├── database.py             # SQLite schema, session-scoped CRUD & TTL cleaner
│   ├── document_processing.py  # Multi-format parsers & sliding-window chunker
│   ├── embeddings.py           # Multi-tenant vector space model & cosine similarity engine
│   └── search.py               # RAG prompt construction & LLM inference dispatch
├── src/                        # Frontend React Components
│   ├── App.jsx                 # Application orchestrator, theme & purge session modal
│   ├── ChatArea.jsx            # Chat feed, hero starter cards & floating input dock
│   ├── ChatMessage.jsx         # Rich markdown renderer, code copy action & avatar
│   ├── DocumentItem.jsx        # Document card, file-type badges & chunk counter
│   ├── Sidebar.jsx             # Knowledge manager, drag & drop zone, search bar
│   ├── SourcePanel.jsx         # Grounding citations, relevance bars & excerpt viewer
│   ├── StatusBadge.jsx         # Pulse indicator badge system
│   ├── api.js                  # Asynchronous API client with automatic session header
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

4. **Configure environment variables** *(Optional for AI answer synthesis)*:
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
   py -X utf8 -m uvicorn main:app --host 127.0.0.1 --port 8000 --reload
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

## 🔑 Environment Variables

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

All requests accept the `X-Session-ID` header to isolate operations per user session.

### Health & Diagnostics
* **`GET /health`**  
  Returns system status, active LLM provider, and multi-tenant isolation status.

### Document Management
* **`GET /documents`**  
  Returns all documents indexed under the requester's session.
* **`POST /upload-document`**  
  `multipart/form-data` payload. Uploads a file, extracts text, chunks it, and indexes vector embeddings under the active session.
* **`GET /documents/{id}`**  
  Fetches status and metadata for a specific document in the session.
* **`DELETE /documents/{id}`**  
  Permanently erases a document, its disk file, and its vector embeddings.
* **`POST /session/reset`**  
  Immediately destroys all uploaded files, SQLite chunks, and vector memory for the requester's session.

### Semantic Search & Conversational RAG
* **`POST /search`**  
  Performs similarity search strictly against the requester's session documents.
  ```json
  {
    "query": "What are the key financial highlights?",
    "top_k": 5,
    "document_ids": [1, 2]
  }
  ```

* **`POST /chat`**  
  Executes full RAG: retrieves relevant chunks, injects context, and generates grounded answers using the active LLM.
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

### Frontend (Netlify / Vercel)
The repository includes a [`netlify.toml`](./netlify.toml) pre-configured with Python 3.11 build settings:
1. Connect your repository to **Netlify**.
2. **Build Command**: `npm run build`
3. **Publish Directory**: `dist`
4. Set `VITE_API_URL` under **Site Settings** → **Environment Variables** pointing to your backend URL.

### Backend (Render / Railway / Docker)
1. Deploy as a Python Web Service.
2. **Start Command**: `uvicorn main:app --host 0.0.0.0 --port $PORT`
3. Configure `GROQ_API_KEY` or `OPENAI_API_KEY` in your hosting dashboard.

---

## 📄 License

Distributed under the **MIT License**. See `LICENSE` for more information.
