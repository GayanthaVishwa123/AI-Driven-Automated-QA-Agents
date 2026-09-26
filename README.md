# TestPulse AI

### Dynamic QA Strategy, Persona User Journeys & Automation Test Code Generator

TestPulse AI is a native AI-driven quality engineering platform that turns
OpenAPI/Swagger specifications and product requirements into a risk-based QA
strategy, persona-focused user journeys, and executable PyTest or Playwright
automation code.

The platform combines a Next.js dashboard, a Python FastAPI service, Docker
Compose, and direct Google Gemini orchestration. It is designed to make
specification-driven testing faster, more traceable, and more actionable
before implementation reaches production.

[![Next.js](https://img.shields.io/badge/Next.js-14-black?logo=next.js)](https://nextjs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.5-3178C6?logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.110-009688?logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com/)
[![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?logo=docker&logoColor=white)](https://www.docker.com/)
[![Gemini AI](https://img.shields.io/badge/Google%20Gemini-AI-4285F4?logo=google&logoColor=white)](https://ai.google.dev/)
[![PyTest](https://img.shields.io/badge/PyTest-8.1-0A9EDC?logo=pytest&logoColor=white)](https://pytest.org/)
[![Playwright](https://img.shields.io/badge/Playwright-1.42-2EAD33?logo=playwright&logoColor=white)](https://playwright.dev/)

> **Project status:** The core dynamic flow is implemented: specification
> ingestion, Gemini-backed strategy and persona generation, code generation,
> and visible frontend error handling. Generated tests must be reviewed and
> executed in an isolated environment before use against production systems.

## Why TestPulse AI?

Traditional QA planning often separates requirements analysis, risk review,
test design, and automation authoring across multiple tools and handoffs.
TestPulse AI keeps those artifacts connected:

```text
Specification
     │
     ▼
Risk-based strategy ──► Persona journeys ──► Executable test suites
     │                         │                    │
     └──────────────► Traceable QA decisions ◄──────┘
```

Every generation request is based on the specification and strategy currently
submitted by the user. The frontend clears stale results before a new
analysis, while the backend surfaces Gemini and validation failures instead of
silently presenting mock success data.

## Key capabilities and workflow

### Stage 1 — Specification Ingestion

- Paste or upload raw OpenAPI/Swagger JSON or YAML.
- Submit PRD and product-requirement text.
- Validate OpenAPI JSON syntax in the browser before analysis.
- Send the exact submitted content to the FastAPI strategy endpoint.
- Clear previous strategy state immediately when a new analysis starts.

### Stage 2 — Strategy and Personas

- Analyze the submitted specification with a Gemini-powered RAG strategy
  agent.
- Identify shift-left risks across functional, security, boundary, negative,
  and concurrency concerns.
- Calculate a specification completeness score.
- Produce prioritized high-level scenarios and test objectives.
- Expand scenarios into persona-based workflows, including:
  - Admin and superuser journeys
  - Regular user journeys
  - Malicious attacker and security-testing journeys
  - Power-user and edge-case journeys
- Normalize heterogeneous model payloads for reliable dashboard rendering.

### Stage 3 — Code Studio

- Send the current strategy and generated persona test cases to the backend.
- Generate runnable Python automation for:
  - API testing with PyTest and `requests`
  - UI testing with Playwright
- Chunk large persona workflows to reduce prompt size and response truncation.
- Strip accidental Markdown code fences before presenting generated code.
- Display backend generation errors instead of replacing failures with a
  default health-check snippet.

### Stage 4 — Execution and RCA

- Submit generated code and execution context to the test-runner workflow.
- Capture execution status and failure output.
- Use the RCA agent to classify likely application, environment, flaky-test,
  or authentication failures.
- Present confidence, failed-line context, probable causes, and suggested
  remediation in the dashboard.

## Technical architecture

```text
┌─────────────────────────────────────────────────────────────────┐
│                     Next.js 14 Dashboard                        │
│  SpecIngestion → StrategyView → CodeStudio → RCAHub             │
│  TypeScript · Tailwind CSS · no-store API client                │
└───────────────────────────────┬─────────────────────────────────┘
                                │ HTTP / JSON
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                         FastAPI Service                          │
│  /api/v1/generate-strategy                                      │
│  /api/v1/generate-code                                          │
│  /api/v1/run-tests                                              │
│  /health                                                        │
└───────────────────────────────┬─────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                   Native Gemini Agent Runtime                    │
│  RAG strategy · persona workflows · code generation · RCA        │
│  Direct google-generativeai integration                         │
└─────────────────────────────────────────────────────────────────┘
```

### Key engineering highlights

- **Native LLM orchestration:** Agents call Google Gemini directly through
  `google-generativeai`, avoiding unnecessary framework bloat and keeping
  prompts, parsing, and failure boundaries visible.
- **Dynamic model configuration:** The active Gemini Flash model is configured
  through environment settings and can be changed without editing frontend
  code. The deployment can target Gemini 2.0 Flash or another enabled Gemini
  Flash model according to project availability and quota.
- **Rate-limit resilience:** Gemini quota and 429 responses are recognized,
  retried with bounded waits where appropriate, and returned as explicit HTTP
  429 errors when the quota remains exhausted.
- **Strict response handling:** Strategy/persona responses are cleaned of
  accidental Markdown fences and parsed as JSON. Invalid model output raises a
  visible generation error.
- **Payload-safe workflow chunking:** Large specifications and persona
  collections are split into bounded chunks and merged deterministically.
- **No silent mock success:** Generation failures are not converted into
  health-check snippets, placeholder personas, or fake strategy results.
- **Browser-safe networking:** The browser uses the published backend address
  (`http://localhost:8000` by default), while Docker services communicate
  through Compose networking.
- **Cache control:** Frontend requests use `cache: "no-store"` and
  `Cache-Control: no-cache` so a new specification cannot reuse an older
  response.
- **Containerized delivery:** Frontend and backend services run together with
  Docker Compose and can be rebuilt consistently in CI or locally.

## Tech stack

| Layer | Technologies |
| --- | --- |
| Frontend | Next.js 14, React 18, TypeScript, Tailwind CSS |
| Backend | Python, FastAPI, Pydantic, Uvicorn, Requests |
| AI engine | Google Gemini API through `google-generativeai` |
| Test generation | PyTest, Requests, Playwright |
| Configuration | `python-dotenv`, environment-based secrets |
| DevOps | Docker, Docker Compose, GitHub Actions |

## Quickstart

### Prerequisites

- Docker Engine and Docker Compose v2
- A Google Gemini API key with an enabled model and available quota
- Git

Local Python and Node.js installations are only required when running the
services outside Docker.

### 1. Clone the repository

```bash
git clone https://github.com/GayanthaVishwa123/AI-Driven-Automated-QA-Agents.git
cd AI-Driven-Automated-QA-Agents
```

### 2. Configure the Gemini key

Create a root `.env` file. The file is ignored by Git and must never be
committed:

```bash
cp .env.example .env
chmod 600 .env
```

Set the key and optional model configuration:

```dotenv
GEMINI_API_KEY=replace-with-your-gemini-api-key
LLM_MODEL=gemini-2.0-flash
```

Use a model that is enabled for the Google project associated with the key.
Model availability and free-tier quotas vary by project and region. Do not
place the key in `frontend/`, browser code, screenshots, or source control.

### 3. Start the complete stack

```bash
docker compose up --build -d
```

The services are available at:

- **Frontend:** <http://localhost:3000>
- **Backend health:** <http://localhost:8000/health>
- **FastAPI Swagger UI:** <http://localhost:8000/docs>
- **FastAPI OpenAPI schema:** <http://localhost:8000/openapi.json>

Check service status and logs:

```bash
docker compose ps
docker compose logs -f backend
```

Verify that the backend container received a key without printing it:

```bash
docker compose exec backend sh -c \
  'test -n "$GEMINI_API_KEY" && echo "GEMINI_API_KEY is configured" || echo "GEMINI_API_KEY is missing"'
```

Stop the stack:

```bash
docker compose down
```

## Local development without Docker

### Backend

```bash
cd backend
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

### Frontend

```bash
cd frontend
npm install
npm run dev
```

When running the frontend locally, `NEXT_PUBLIC_BACKEND_URL` should point to
the browser-reachable backend, normally:

```bash
NEXT_PUBLIC_BACKEND_URL=http://localhost:8000
```

## API surface

| Method | Endpoint | Purpose |
| --- | --- | --- |
| `GET` | `/health` | Service health check |
| `POST` | `/api/v1/generate-strategy` | Analyze PRD/OpenAPI input and generate strategy plus personas |
| `POST` | `/api/v1/generate-code` | Generate PyTest and Playwright code from strategy workflows |
| `POST` | `/api/v1/run-tests` | Submit generated test code for execution/RCA workflow |
| `POST` | `/api/v1/ingestion` | Ingestion contract endpoint |
| `POST` | `/api/v1/personas` | Persona workflow contract endpoint |

The primary Stage 1 request shape is:

```json
{
  "spec_type": "swagger",
  "swagger_spec": "{ \"openapi\": \"3.0.0\", \"paths\": {} }",
  "focus_areas": []
}
```

The Stage 3 request includes the complete strategy payload:

```json
{
  "strategy_data": {
    "project_name": "Example API",
    "high_level_scenarios": [],
    "persona_workflows": []
  },
  "target_url": "http://localhost:8000"
}
```

## Repository structure

```text
AI-Driven-Automated-QA-Agents/
├── backend/
│   ├── app/
│   │   ├── agents/
│   │   │   ├── codegen_agent.py       # PyTest/Playwright generation
│   │   │   ├── persona_agent.py       # Persona workflow generation
│   │   │   ├── rag_agent.py           # Strategy and risk generation
│   │   │   └── rca_agent.py           # Failure analysis
│   │   ├── api/v1/
│   │   │   ├── codegen.py             # /generate-code
│   │   │   ├── ingestion.py           # Ingestion endpoint
│   │   │   ├── personas.py            # Persona endpoint
│   │   │   ├── rca.py                 # Test/RCA endpoint
│   │   │   └── strategy.py            # /generate-strategy
│   │   ├── core/
│   │   │   ├── config.py              # Environment settings
│   │   │   └── llm_factory.py         # Gemini client factory
│   │   ├── schemas/
│   │   │   ├── input_schema.py        # Request models
│   │   │   └── output_schema.py       # Response models
│   │   ├── services/
│   │   │   └── test_runner.py         # Execution service boundary
│   │   └── main.py                    # FastAPI application
│   ├── Dockerfile
│   └── requirements.txt
├── frontend/
│   ├── src/
│   │   ├── app/
│   │   │   ├── page.tsx               # Dashboard workflow and API triggers
│   │   │   └── layout.tsx
│   │   ├── components/
│   │   │   ├── dashboard/
│   │   │   │   ├── SpecIngestion.tsx
│   │   │   │   ├── StrategyView.tsx
│   │   │   │   ├── CodeStudio.tsx
│   │   │   │   └── RCAHub.tsx
│   │   │   └── layout/
│   │   └── lib/
│   │       ├── api-client.ts          # no-store backend client
│   │       └── types.ts
│   ├── Dockerfile
│   ├── package.json
│   └── tailwind.config.ts
├── .github/workflows/ci-cd.yml
├── .env.example
├── .gitignore
├── docker-compose.yml
└── README.md
```

## Testing and quality checks

Backend tests:

```bash
cd backend
pytest
```

Frontend lint and production build:

```bash
cd frontend
npm run lint
npm run build
```

Compose validation:

```bash
docker compose config --quiet
```

Generated automation code should be reviewed before execution. For secure
deployments, execute generated tests in an isolated worker with restricted
network access, resource limits, and an explicit allowlist of target systems.

## Security and operational guidance

- Rotate any API key that has been exposed in terminal output, chat, logs, or
  source files.
- Keep `.env` out of Git and use a managed secret store in production.
- Never expose `GEMINI_API_KEY` through `NEXT_PUBLIC_*` variables.
- Treat uploaded specifications and generated code as untrusted input.
- Do not execute generated code inside the FastAPI request process.
- Add authentication, authorization, tenant isolation, audit logging, and
  request quotas before exposing the service publicly.
- Monitor Gemini usage and configure retry/backoff budgets so quota errors do
  not cause uncontrolled request amplification.

## Roadmap

- Add durable run history and specification versioning.
- Add authentication, workspace isolation, and role-based access control.
- Add isolated test-execution workers with artifact storage.
- Add OpenAPI/YAML parsing and richer endpoint-to-test traceability.
- Add streaming generation progress and per-chunk observability.
- Add provider/model capability discovery and configurable model routing.
- Add CI quality gates for generated test syntax and schema validation.

## License

Add the project license that matches your intended distribution before
publishing this repository as an open-source portfolio project.
