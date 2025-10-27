# AI Digital Twin — Monorepo

Small monorepo with a FastAPI backend and a Next.js frontend implementing an AI "digital twin" chat application with on-disk memory.

## Repo layout

- backend/ — FastAPI backend
  - server.py
  - lambda_handler.py (Mangum wrapper for AWS Lambda)
  - requirements.txt, pyproject.toml
  - me.txt (system prompt/personality)
- frontend/ — Next.js frontend (React)
  - components/twin.tsx
  - app/page.tsx
  - package.json
- memory/ — persisted JSON memories
- README.md — this file

---

## Prerequisites

- macOS
- Python 3.13 (project requires >=3.13)
  - Recommended: pyenv for per-project Python versions
- Node.js (LTS) and npm or pnpm
- Git
- OpenAI API key (export as OPENAI_API_KEY)

---

## Backend — install & run

From repo root:

1. Install Python 3.13 (pyenv recommended)
```bash
# install pyenv if missing (Homebrew)
brew update
brew install pyenv

# install Python 3.13.x and set locally
pyenv install 3.13.0
pyenv local 3.13.0
python --version
```

2. Create venv and install dependencies
```bash
cd backend
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
# or if package configured:
# pip install -e .
```

3. Configure environment (backend/.env or export)
```
OPENAI_API_KEY=sk-...
CORS_ORIGINS=http://localhost:3000
MEMORY_DIR=../memory  # optional
```

4. Run development server
```bash
# from backend/
uvicorn server:app --reload --host 0.0.0.0 --port 8000
# or
python server.py
```

Health checks:
```bash
curl http://localhost:8000/health
curl http://localhost:8000/sessions
```

Lambda note:
- backend/lambda_handler.py wraps `server.app` with Mangum for AWS Lambda.
- Set handler to `lambda_handler.handler` when deploying Lambda (or package as container).

---

## Frontend — install & run

From repo root:

1. Install and run dev server
```bash
cd frontend
npm install
npm run dev
```

2. Open the app:
```
http://localhost:3000
```

3. Build for production
```bash
npm run build
npm start
```

Frontend expects backend at http://localhost:8000 by default. If the frontend uses a configured API URL, set it in environment or NEXT_PUBLIC_API_URL accordingly.

---

## Common problems & fixes

1) Python version mismatch (error: requested 3.12 incompatible with requires-python >=3.13)
- Ensure you run Python 3.13. Use pyenv or Homebrew-installed Python.

2) Frontend appears empty or shows a pointer/arrow on GitHub
- Likely causes: frontend is a git submodule, a symlink, or contains a nested .git directory.

Diagnostics (run from repo root):
```bash
ls -la frontend
readlink frontend 2>/dev/null || true
cat .gitmodules 2>/dev/null || true
git submodule status 2>/dev/null || true
ls -la frontend/.git 2>/dev/null || true
```

If you have a backup at frontend/frontend-backup and want to restore the real frontend as a normal directory:

Safe restore (preserve dotfiles, exclude .git):
```bash
# from repo root
cd frontend
rsync -av --exclude='.git' frontend-backup/ .
ls -la  # verify contents present
rm -rf frontend-backup

# commit restored content
cd ..
git add frontend
git commit -m "Restore frontend from backup"
git push
```

Convert a submodule to a normal directory (if frontend was a submodule)
```bash
# backup first if unsure
cp -R frontend frontend-backup

# remove submodule metadata (ignore errors)
git submodule deinit -f frontend 2>/dev/null || true
git rm -f frontend 2>/dev/null || true
git config -f .gitmodules --remove-section submodule.frontend 2>/dev/null || true
git add .gitmodules 2>/dev/null || true
rm -rf .git/modules/frontend

# restore files and commit
mv frontend-backup frontend
git add frontend
git commit -m "Convert frontend submodule to normal directory"
git push
```

If frontend contains its own .git and you want it tracked by the top-level repo:
```bash
rm -rf frontend/.git
git add frontend
git commit -m "Track frontend inside main repo"
git push
```

3) Files ignored by .gitignore
```bash
git status --ignored frontend
git ls-files frontend
```
- If files are ignored accidentally, remove the ignore rule, then git add and commit.

---

## Troubleshooting checklist to paste here if you want help

Paste the outputs of:
```bash
ls -la frontend
readlink frontend 2>/dev/null || true
cat .gitmodules 2>/dev/null || true
git submodule status 2>/dev/null || true
git status --porcelain
```

---

## Notes & tips

- Do not commit OPENAI_API_KEY or other secrets. Use .env and .gitignore.
- memory/ contains JSON memory files; back up if important.
- For CI (GitHub Actions), ensure the runner uses Python 3.13 in matrix.
- If deploying to AWS Lambda use Mangum wrapper and package dependencies (or use a container image).

---

If you want this README committed to the repository now, I can write the file changes for you.