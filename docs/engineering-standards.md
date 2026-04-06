# Engineering Standards — Welday AI

> Version 1.1 | Last updated: 2026-03-31

## Overview

These standards apply to all client delivery projects. Every new project must use an approved scaffolding template from this repo. Deviations require documented justification.

---

## Tech Stack (Defaults)

| Layer | Choice | Version | Notes |
|-------|--------|---------|-------|
| Language | Python | 3.12 | Use `uv` for package management |
| API framework | FastAPI | ≥0.111 | Async by default |
| Frontend | React + TypeScript | React 18, TS 5 | Vite for bundling |
| LLM integration | LangChain | ≥0.2 | LlamaIndex for complex RAG |
| Hosting | Vercel | Latest | Frontend + API deployment target |
| Database | Neon Postgres + pgvector | Latest | Managed Postgres baseline |
| Containers | Docker + Docker Compose | — | Local dev parity |
| JS package mgr | pnpm | ≥9 | Lockfile committed |
| Python pkg mgr | uv | Latest | `uv.lock` committed |

---

## Project Structure

All projects follow the template from `templates/<type>/`. See:
- `templates/rag-assistant/` — RAG knowledge assistant
- `templates/api-service/` — Pure backend API
- `templates/data-pipeline/` — ETL / data processing

---

## Branching Conventions

```
main          — production-ready, protected (no direct push)
feature/*     — new features (feature/rag-ingestion-v2)
fix/*         — bug fixes (fix/embedding-null-check)
chore/*       — maintenance (chore/update-langchain)
release/*     — release prep branches
```

- All changes go through PRs — no direct commits to `main`
- Branch names are lowercase, hyphen-separated
- Delete merged branches

---

## Pull Request Checklist

When opening a PR, include this checklist:

```markdown
## Summary
<!-- What does this PR do? -->

## Changes
- [ ] Feature / Fix / Chore / Docs

## Testing
- [ ] Unit tests added / updated
- [ ] Integration tests pass locally
- [ ] `make test` passes with ≥80% coverage
- [ ] No new `mypy` errors

## Security
- [ ] No secrets or credentials committed
- [ ] `detect-secrets` scan clean
- [ ] Dependencies checked for known CVEs

## Ops
- [ ] Vercel project/env configuration reviewed
- [ ] `.env.example` updated (if new env vars added)
- [ ] README updated (if setup steps changed)
```

---

## Code Quality Standards

### Python
- Formatter: `ruff format` (replaces black)
- Linter: `ruff check` (replaces flake8/pylint)
- Type checks: `mypy --strict` for library code, `--ignore-missing-imports` for scripts
- Test framework: `pytest` with `pytest-asyncio` for async code
- Coverage gate: **80% minimum** enforced in CI

### JavaScript / TypeScript
- Formatter: Prettier (config in `prettier.config.js`)
- Linter: ESLint with TypeScript plugin
- Test framework: Vitest (unit), Playwright (e2e)

## Pre-Commit Hooks

All projects include `.pre-commit-config.yaml`. Run `pre-commit install` after cloning.

Hooks enforced:
1. `ruff` — lint + format Python
2. `mypy` — type checking
3. `detect-secrets` — block credential commits
4. `eslint` + `prettier` (if frontend/ exists)
5. `trailing-whitespace` + `end-of-file-fixer`

---

## CI/CD Pipelines (GitHub Actions)

Every project includes these workflows in `.github/workflows/`:

| Workflow | Trigger | Purpose |
|----------|---------|---------|
| `lint.yml` | PR open/update | Ruff, mypy, ESLint |
| `test.yml` | PR open/update | pytest with coverage gate |
| `build.yml` | Push/PR | Verify Docker build artifacts |
| `deploy.yml` | PR + Push to main | Vercel preview + production deployment |

---

## Secrets Management

| Environment | Method |
|-------------|--------|
| Local dev | `.env` file (gitignored), `.env.example` committed |
| CI/CD | GitHub Actions Secrets |
| Staging/Prod | Vercel project env vars + Neon managed credentials |

Required GitHub secrets for template deploys:
- `VERCEL_TOKEN`
- `VERCEL_ORG_ID`
- `VERCEL_PROJECT_ID`
- `DATABASE_URL`

Never commit:
- API keys, tokens, passwords
- Private keys or certificates
- Client-specific configuration
- Database connection strings with credentials

---

## Local Dev Setup

### Prerequisites
```bash
# Python toolchain
curl -LsSf https://astral.sh/uv/install.sh | sh

# Node toolchain
curl -fsSL https://fnm.vercel.app/install | bash
fnm use --install-if-missing 20

# direnv (auto-load .env)
# macOS: brew install direnv
# Ubuntu: sudo apt install direnv
echo 'eval "$(direnv hook bash)"' >> ~/.bashrc

# Docker Desktop or docker + docker compose

# pre-commit
uv tool install pre-commit
```

### New Project Quickstart
```bash
git clone git@github.com:welday-ai/delivery-templates.git
cp -r delivery-templates/templates/rag-assistant my-client-project
cd my-client-project
cp .env.example .env
# edit .env with your values
uv sync
pre-commit install
make dev
```

---

## Makefile Standard Targets

Every project `Makefile` must include these targets:

```makefile
make dev       # Start local dev environment (docker compose up + hot reload)
make test      # Run test suite with coverage
make lint      # Run all linters
make format    # Auto-format code
make build     # Build Docker image
make deploy    # Trigger deploy guidance (CI via Vercel workflow)
make clean     # Remove build artifacts and containers
```

---

## Client Delivery Notes

- Never commit client-specific configs to this repo
- All templates are generic — parameterize with `<CLIENT_NAME>`, `<PROJECT_NAME>`
- When starting a client engagement, fork/copy the template to a new private repo
- Add `DELIVERY.md` to each client repo with client-specific setup notes
