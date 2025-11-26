# 🎓 Chatbot RAG Universitas Gunadarma

<div align="center">

![Python](https://img.shields.io/badge/Python-3.12+-blue?logo=python)
![React](https://img.shields.io/badge/React-18.2-blue?logo=react)
![TypeScript](https://img.shields.io/badge/TypeScript-5.9-blue?logo=typescript)
![FastAPI](https://img.shields.io/badge/FastAPI-0.115-green?logo=fastapi)
![License](https://img.shields.io/badge/license-MIT-green)

**An intelligent AI-powered chatbot system designed to provide accurate, context-aware academic information for Universitas Gunadarma using Retrieval-Augmented Generation (RAG) architecture.**

[Features](#-features) • [Architecture](#-architecture) • [Getting Started](#-getting-started) • [Deployment](#-deployment)

</div>

---

## 📖 Overview

This monorepo contains a complete RAG-based chatbot system that helps students, faculty, and staff access academic and campus-related information from Universitas Gunadarma. The system leverages modern AI technologies to reduce hallucinations common in Large Language Models (LLMs) by grounding responses in factual, university-specific data.

### Project Goals

- **Accurate Information Delivery**: Provide reliable answers backed by verified university data sources
- **Reduced LLM Hallucinations**: Use RAG architecture to ground responses in factual content
- **Scalable & Performant**: Implement hybrid search, semantic caching, and async processing
- **Modern User Experience**: Offer both Chainlit and React-based interfaces
- **Production-Ready**: Containerized deployment with Docker support

---

## ✨ Features

### Core Capabilities

- **Retrieval-Augmented Generation (RAG)**: Combines semantic search with LLM generation for accurate responses
- **Hybrid Search**: Merges vector similarity search (embeddings) with keyword-based search (TF-IDF)
- **Semantic Caching**: Intelligently caches similar queries to reduce latency and API costs
- **Automated Web Crawling**: Periodically updates knowledge base from official university websites
- **Source Attribution**: Every response includes references to source documents for verification
- **Real-time Communication**: WebSocket support for interactive chat experiences
- **Multiple Frontend Options**: Choose between Chainlit (Python) or React (TypeScript) interfaces

### Technical Highlights

- **Clean Architecture**: Domain-driven design with clear separation of concerns
- **Async Processing**: Built on ASGI for handling concurrent requests efficiently
- **Health Monitoring**: Comprehensive healthchecks for all services
- **Persistent Storage**: Volume mounts for data, cache, and logs
- **Production-Grade**: Docker Compose orchestration with restart policies and dependency management

---

## 🏗️ Architecture

### System Architecture

```mermaid
graph TB
    subgraph "Frontend Layer"
        FR[React Frontend<br/>Port 8080]
        FC[Chainlit Frontend<br/>Port 8080]
    end

    subgraph "Backend Layer"
        API[FastAPI Server<br/>Port 8000]
        RAG[RAG Pipeline]
        CRAWL[Web Crawler]
    end

    subgraph "Data Layer"
        VDB[(Vector DB<br/>PGVector)]
        CACHE[(Semantic Cache)]
        FILES[File Storage<br/>data/cache/logs]
    end

    subgraph "External Services"
        LLM[Google Gemini AI]
        WEB[University Websites]
    end

    FR -->|HTTP/WS| API
    FC -->|HTTP/WS| API
    API --> RAG
    RAG --> VDB
    RAG --> CACHE
    RAG --> LLM
    CRAWL --> WEB
    CRAWL --> FILES
    FILES --> RAG
```

### Monorepo Structure

This repository is organized as a **Git supermodule** containing three independent submodules:

```
gunadarma-ai/                          # Supermodule (this repository)
├── backend/                            # Submodule: RAG backend service
├── frontend-chainlit/                  # Submodule: Chainlit-based UI
├── frontend-react/                     # Submodule: React-based UI
├── docker-compose.yml                  # Production deployment config (React)
├── docker-compose-chainlit.yml         # Production deployment config (Chainlit)
└── LICENSE
```

### Submodules Overview

| Submodule             | Purpose                     | Tech Stack                           | Repository                                                                                     |
| --------------------- | --------------------------- | ------------------------------------ | ---------------------------------------------------------------------------------------------- |
| **backend**           | RAG engine & API server     | Python, FastAPI, LangChain, PGVector | [chatbot-rag-gunadarma-backend](https://github.com/maybeitsai/chatbot-rag-gunadarma-backend)   |
| **frontend-chainlit** | Python-based chat interface | Python, Chainlit, Clean Architecture | [chatbot-rag-gunadarma-frontend](https://github.com/maybeitsai/chatbot-rag-gunadarma-frontend) |
| **frontend-react**    | Modern web interface        | React, TypeScript, TailwindCSS, Vite | [client-gunadarma-ai](https://github.com/maybeitsai/client-gunadarma-ai)                       |

---

## 🚀 Getting Started

### Prerequisites

- **Git** (with submodule support)
- **Docker** & **Docker Compose** (for containerized deployment)
- **Python 3.12+** (for local backend development)
- **Node.js 18+** or **Bun** (for local React frontend development)

### Installation

#### 1. Clone the Monorepo with Submodules

```bash
# Clone the main repository with all submodules
git clone --recursive https://github.com/maybeitsai/gunadarma-ai.git
cd gunadarma-ai

# If you already cloned without --recursive, initialize submodules:
git submodule init
git submodule update
```

#### 2. Update Submodules (when needed)

```bash
# Pull latest changes for all submodules
git submodule update --remote --merge

# Or update a specific submodule
cd backend
git pull origin main
cd ..
```

#### 3. Environment Configuration

Each service requires its own `.env` file:

**Backend (`backend/.env`):**

```env
GOOGLE_API_KEY=your_gemini_api_key
DATABASE_URL=postgresql://user:password@localhost:5432/dbname
# Additional backend config...
```

**Frontend React (`frontend-react/.env`):**

```env
VITE_API_BASE_URL=http://localhost:8000
```

**Frontend Chainlit (`frontend-chainlit/.env`):**

```env
BACKEND_URL=http://localhost:8000
API_TIMEOUT=60.0
```

> **Note**: See individual submodule READMEs for complete environment variable documentation.

---

## 🐳 Deployment

### Option 1: Docker Compose (Recommended)

#### Deploy with React Frontend

```bash
# Start backend + React frontend
docker-compose up -d

# Check service status
docker-compose ps

# View logs
docker-compose logs -f
```

- **Backend API**: http://localhost:8000
- **React Frontend**: http://localhost:8080
- **API Documentation**: http://localhost:8000/docs

#### Deploy with Chainlit Frontend

```bash
# Start backend + Chainlit frontend
docker-compose -f docker-compose-chainlit.yml up -d

# Check service status
docker-compose -f docker-compose-chainlit.yml ps
```

- **Backend API**: http://localhost:8000
- **Chainlit Frontend**: http://localhost:8080

#### Service Management

```bash
# Stop services
docker-compose down

# Stop and remove volumes
docker-compose down -v

# Rebuild after code changes
docker-compose up -d --build

# View resource usage
docker stats
```

### Option 2: Local Development

#### Run Backend

```bash
cd backend

# Install dependencies with uv
uv venv
uv sync

# Initialize the system (crawl, process, index)
python -m scripts.run setup --all

# Start the server
uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

#### Run React Frontend

```bash
cd frontend-react

# Install dependencies
bun install

# Start development server
bun run dev
```

#### Run Chainlit Frontend

```bash
cd frontend-chainlit

# Install dependencies
uv venv
uv sync

# Start Chainlit server
uv run chainlit run app.py -w
```

---

## 📦 Tech Stack Summary

### Backend

- **Framework**: FastAPI (ASGI web framework)
- **AI/LLM**: Google Generative AI (Gemini), LangChain
- **Vector Database**: PGVector (PostgreSQL extension)
- **Search**: Hybrid (FAISS/PGVector + TF-IDF)
- **Web Scraping**: BeautifulSoup4, Playwright
- **Package Manager**: uv (fast Python package installer)

### Frontend (React)

- **Framework**: React 18 with TypeScript
- **Build Tool**: Vite
- **Runtime**: Bun
- **UI Library**: shadcn/ui (Radix UI + TailwindCSS)
- **Styling**: TailwindCSS
- **Icons**: Lucide React

### Frontend (Chainlit)

- **Framework**: Chainlit (Python chat UI framework)
- **Architecture**: Clean Architecture (DDD)
- **HTTP Client**: httpx (async)
- **Package Manager**: uv

### Infrastructure

- **Containerization**: Docker, Docker Compose
- **Reverse Proxy**: (Optional) Nginx/Traefik
- **CI/CD**: GitHub Actions (future integration)

---

## 🔧 Development Workflow

### Working with Submodules

```bash
# Check submodule status
git submodule status

# Update all submodules to latest commits
git submodule update --remote --merge

# Make changes in a submodule
cd backend
git checkout -b feature/new-feature
# ... make changes ...
git add .
git commit -m "Add new feature"
git push origin feature/new-feature

# Update supermodule to point to new submodule commit
cd ..
git add backend
git commit -m "Update backend submodule"
git push
```

### Running Tests

```bash
# Backend tests
cd backend
uv run python -m pytest tests/ -v

# Frontend tests (if available)
cd frontend-react
bun test
```

---

## 📊 Environment Variables

### Backend Required Variables

| Variable         | Description                  | Example                               |
| ---------------- | ---------------------------- | ------------------------------------- |
| `GOOGLE_API_KEY` | Google Gemini API key        | `AIza...`                             |
| `DATABASE_URL`   | PostgreSQL connection string | `postgresql://user:pass@host:5432/db` |

### Frontend Variables

| Variable                    | Description     | Default                 |
| --------------------------- | --------------- | ----------------------- |
| `BACKEND_URL` (Chainlit)    | Backend API URL | `http://localhost:8000` |
| `VITE_API_BASE_URL` (React) | Backend API URL | `http://localhost:8000` |

See individual submodule `.env.example` files for complete configuration options.

---

## 🤝 Contributing

Contributions are welcome! Since this is a monorepo with submodules:

1. **Fork the appropriate repository** (supermodule or specific submodule)
2. **Create a feature branch** (`git checkout -b feature/amazing-feature`)
3. **Make your changes** and commit (`git commit -m 'Add amazing feature'`)
4. **Push to your fork** (`git push origin feature/amazing-feature`)
5. **Open a Pull Request** to the respective repository

### Contribution Guidelines

- Follow existing code style and architecture patterns
- Add tests for new features
- Update documentation as needed
- Ensure Docker builds succeed
- Test with both frontend options if applicable

---

## 📄 License

This project is licensed under the **MIT License** - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgements

- **Universitas Gunadarma** - For the institutional context and academic data
- **Google Gemini** - For powering the AI language model
- **LangChain** - For RAG orchestration framework
- **Chainlit** - For the interactive Python chat interface
- **shadcn/ui** - For beautiful React components
- **FastAPI** - For the high-performance API framework

---

## 📞 Support

For issues, questions, or contributions:

- **Backend Issues**: [chatbot-rag-gunadarma-backend/issues](https://github.com/maybeitsai/chatbot-rag-gunadarma-backend/issues)
- **Chainlit Frontend**: [chatbot-rag-gunadarma-frontend/issues](https://github.com/maybeitsai/chatbot-rag-gunadarma-frontend/issues)
- **React Frontend**: [client-gunadarma-ai/issues](https://github.com/maybeitsai/client-gunadarma-ai/issues)

---

<div align="center">

**Built with ❤️ for Universitas Gunadarma**

[![GitHub](https://img.shields.io/badge/GitHub-maybeitsai-black?logo=github)](https://github.com/maybeitsai)

</div>
