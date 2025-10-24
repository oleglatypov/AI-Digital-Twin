# AI Digital Twin — Monorepo

This repo contains a Next.js frontend and a FastAPI backend that implement an "AI Digital Twin" chat application with on-disk memory.

Quick links:
- Backend server: [backend/server.py](backend/server.py)
- Frontend app / UI: [frontend/components/twin.tsx](frontend/components/twin.tsx)
- Example memory: [memory/8e0e269a-f155-45ce-a895-e6a9db32ba80.json](memory/8e0e269a-f155-45ce-a895-e6a9db32ba80.json)

---

## Prerequisites

- Python >= 3.13 (per [backend/pyproject.toml](backend/pyproject.toml))
- Node.js (LTS recommended) and npm
- An OpenAI API key (export as OPENAI_API_KEY in backend .env)

---

## Backend — setup & run

1. Create and activate a virtual environment, then install dependencies:

```bash
cd backend
python -m venv .venv
# macOS / Linux
source .venv/bin/activate
# Windows (PowerShell)
# .venv\Scripts\Activate.ps1

pip install -r [requirements.txt](http://_vscodecontentref_/14)
# or use pip install -e . if you prefer pyproject-based install