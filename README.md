# Insurance Claim Assistant

AI-native claims operations for the $85B U.S. P&C loss-adjustment-expense pool.

A working, full-stack prototype of an AI-powered insurance claim assistant covering Auto, Homeowners, and General Liability. Built to demonstrate how modern LLM tooling, careful PII handling, and a clean adjudication-adjacent UX can compress claims cycle times and reduce LAE without compromising regulatory posture.

> Built by [Kerry Goode](https://github.com/KgoodeMFTA) — former State Farm Auto Injury Claims Team Manager, currently completing an M.S. in FinTech & Analytics at Wake Forest University.

---

## What it does

- **Guided FNOL intake** — multi-step wizard with LOB-specific fields (VIN/police report for Auto, peril/mortgagee for Homeowners, trigger/incident type for GL)
- **Policy explainer** — paste any policy clause, get a plain-English breakdown with citations
- **Evidence organizer** — upload, tag, OCR, and AI-summarize claim evidence
- **Timeline estimator** — per-LOB, per-stage estimates with state-specific statute-of-limitations awareness
- **AI insights panel** — next-best-action recommendations, FNOL draft generation, status rationale
- **Status board** — six-stage claim FSM (Reported → Investigation → Coverage → Liability → Settlement → Closed)

Heavy AI throughout, with deterministic guardrails: PII redaction before every LLM call, audit logging of all AI decisions, and an LLM gateway abstraction that swaps between OpenAI, Anthropic, and a deterministic mock provider for offline development.

## Tech stack

| Layer       | Technology                                          |
| ----------- | --------------------------------------------------- |
| Frontend    | Next.js 14 (App Router), TypeScript, Tailwind, shadcn/ui |
| Backend     | FastAPI, SQLAlchemy, Alembic, Pydantic              |
| Storage     | SQLite (dev) / Postgres (prod), S3-compatible blob, Redis cache |
| AI          | LLM gateway: OpenAI / Anthropic / Mock              |
| OCR         | Tesseract (default) / AWS Textract                  |
| Auth        | JWT (MVP) — Auth0/Clerk-ready                       |
| Workers     | Celery + Redis                                      |
| Test        | pytest (28 tests passing)                           |
| Container   | Docker, docker-compose                              |

See [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) for the full system diagram.

## Quickstart

### Option 1 — zero-dependency local dev (recommended)

```bash
# Backend
cd backend
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env          # defaults: mock LLM, SQLite
uvicorn app.main:app --reload
# → API at http://localhost:8000  (OpenAPI docs at /docs)

# Frontend (in a new terminal)
cd frontend
npm install
cp .env.example .env.local
npm run dev
# → http://localhost:3000
```

### Option 2 — full stack with Postgres + Redis + MinIO

```bash
cd backend && docker-compose up --build
cd frontend && npm run dev
```

### Run tests

```bash
cd backend && pytest tests/ -v
```

## Project structure

```
.
├── backend/                FastAPI service (28 tests passing)
│   ├── app/
│   │   ├── api/routers/    auth, claims, evidence, policies, ai, timelines
│   │   ├── core/           config, db, security
│   │   ├── models/         SQLAlchemy ORM
│   │   ├── schemas/        Pydantic
│   │   ├── services/       ai_service, pii_redactor, ocr_service, timeline_service
│   │   └── workers/        Celery tasks
│   └── tests/              pytest
├── frontend/               Next.js 14 + TypeScript + Tailwind
│   └── src/
│       ├── app/            dashboard, claims/new, claims/[id], policy-explainer
│       ├── components/     wizard/, claim/
│       └── lib/            api client, utils
└── docs/                   PRD, ARCHITECTURE, API_CONTRACTS, AI_PROMPTS, DATA_MODEL, UX_FLOWS
```

## Documentation

- **[PRD](docs/PRD.md)** — Personas, user stories, scope, success metrics
- **[Architecture](docs/ARCHITECTURE.md)** — System diagrams, data flows, security
- **[Data Model](docs/DATA_MODEL.md)** — Postgres schema with ERD
- **[API Contracts](docs/API_CONTRACTS.md)** — REST endpoints with JSON examples
- **[AI Prompts](docs/AI_PROMPTS.md)** — Prompt library with schemas and guardrails
- **[UX Flows](docs/UX_FLOWS.md)** — Screen-by-screen wireframes

## Compliance posture

Designed for GLBA Safeguards Rule, HIPAA (injury claims), state UCSPA/UPPA (NAIC Model #900), NAIC AI Model Bulletin (2023), Colorado SB21-169, NY DFS Circular Letter 7, and the NY DFS Part 500 control set. PII redaction (SSN, DL, policy numbers, etc.) runs before every LLM call.

> **Note:** ECOA / Regulation B does not apply to insurance claims (Reg B governs lending). It is intentionally out of scope.

## Roadmap

- v1.0 — current scope (Auto / HO / GL FNOL + evidence + policy explainer + status board)
- v1.5 — SIU fraud module, subrogation acceleration, real e-signature integration
- v2.0 — Carrier analytics dashboard, predictive severity, reserve adequacy

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). Security reports: [SECURITY.md](SECURITY.md).

## License

[MIT](LICENSE) — see file for full text.

---

## Disclaimer

This is a portfolio / capstone project. It is not licensed, certified, or audited for production use in any U.S. or international insurance market. The AI features are demonstrations, not adjudication systems. No part of this code should be used in production claims handling without independent regulatory review.
