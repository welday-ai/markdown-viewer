# delivery-templates

Internal delivery templates for Welday AI consulting engagements.

> **Private repo** — do not commit client-specific configs or credentials.

## Contents

| Path | Purpose |
|------|---------|
| `engineering-standards.md` | Tech stack, conventions, PR process, dev setup |
| `templates/rag-assistant/` | RAG Knowledge Assistant starter template |
| `templates/api-service/` | Pure backend API service template |
| `templates/data-pipeline/` | ETL / data pipeline template |

## Quickstart

### Start a new client project from a template

```bash
# Clone this repo
git clone git@github.com:welday-ai/delivery-templates.git

# Copy the appropriate template
cp -r delivery-templates/templates/rag-assistant my-client-project
cd my-client-project

# Set up environment
cp .env.example .env
# Edit .env with project-specific values

# Install dependencies
uv sync
pre-commit install

# Start local dev environment
make dev
```

### Template parameters to replace

Search for these placeholder strings and replace with project-specific values:

- `<PROJECT_NAME>` — project/client name (lowercase, hyphen-separated)
- `<CLIENT_NAME>` — client organization name
- `<ENV>` — environment name (staging, production)

## Template: RAG Assistant

Full-stack RAG knowledge assistant with:
- FastAPI backend with `/query`, `/ingest`, `/health` endpoints
- pgvector for vector storage
- React + TypeScript frontend
- Vercel hosting for frontend + API routes
- Neon Postgres (pgvector) for managed data layer
- GitHub Actions CI/CD (lint → test → build-check → deploy)

See `templates/rag-assistant/` for full structure.

## Standards

Read `engineering-standards.md` before starting any engagement. All projects must follow these standards.

## Adding a New Template

1. Copy `templates/api-service/` as a base
2. Replace placeholders
3. Test locally with `make dev`
4. Add an entry to this README
5. Open a PR for review

## GitHub Org Setup (One-time)

For new GitHub org setup:
1. Create org at github.com/organizations/new — suggested name: `welday-ai`
2. Create this repo as private: `welday-ai/delivery-templates`
3. Configure branch protection on `main` (require PR + 1 review)
4. Set up GitHub Actions secrets: `VERCEL_TOKEN`, `VERCEL_ORG_ID`, `VERCEL_PROJECT_ID`, `DATABASE_URL`
5. Configure Vercel project connection to the GitHub repository
