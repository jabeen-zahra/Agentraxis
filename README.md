<div align="center">

# Agentraxis

### AI-Agent Security Testing &amp; Defense Platform

*A controlled sandbox for demonstrating AI-agent security risks — and proving that backend-enforced authorization can stop them.*

[![Python](https://img.shields.io/badge/Python-3.11+-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![Node.js](https://img.shields.io/badge/Node.js-20+-339933?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org/)
[![React](https://img.shields.io/badge/React-18-61DAFB?style=for-the-badge&logo=react&logoColor=black)](https://react.dev/)
[![FastAPI](https://img.shields.io/badge/FastAPI-backend-009688?style=for-the-badge&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com/)
[![SQLite](https://img.shields.io/badge/SQLite-local%20db-07405E?style=for-the-badge&logo=sqlite&logoColor=white)](https://www.sqlite.org/)
[![OWASP](https://img.shields.io/badge/OWASP-LLM%20Top%2010-000000?style=for-the-badge&logo=owasp&logoColor=white)](https://owasp.org/www-project-top-10-for-large-language-model-applications/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](LICENSE)

</div>

---

## Table of Contents

- [About](#about)
- [Why Agentraxis](#why-agentraxis)
- [Core Concept: Vulnerable vs. Protected Mode](#core-concept-vulnerable-vs-protected-mode)
- [Threat Scenarios](#threat-scenarios)
- [Key Features](#key-features)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Security Boundaries](#security-boundaries)
- [Roadmap](#roadmap)
- [Documentation](#documentation)
- [Team](#team)
- [License](#license)

---

## About

**Agentraxis** *(agent + praxis — putting theory into practice)* is a controlled, educational platform for simulating selected AI-agent security risks and evaluating defensive controls against them.

From a single web dashboard, a tester picks one of three predefined attack scenarios and runs it against simulated agents and mock tools — once in a **vulnerable baseline mode**, and once in a **protected mode** where every tool call is checked against a backend authorization policy. Every allowed and denied action is logged, and the two runs are compared using metrics calculated from real, stored results.

> All scenarios use synthetic data and mock tools only. Agentraxis never touches real systems, real credentials, or a paid AI API to demonstrate its core functionality.

## Why Agentraxis

AI agents are increasingly trusted with tools, data, and the ability to call *other* agents — and that trust surface introduces risks that don't look like classic application vulnerabilities:

| Problem | What usually goes wrong |
|---|---|
| **Prompt injection** | An agent reads untrusted content and treats embedded instructions as commands |
| **Excessive permissions** | An agent is allowed to do more than its role actually needs |
| **Unsafe delegation** | One agent blindly trusts a request forwarded by another agent |

Agentraxis exists to make these risks *visible, reproducible, and measurable* in a safe classroom-scale environment — and to prove, with real audit data, that independent backend authorization (not an agent's own judgment) is what actually stops them.

## Core Concept: Vulnerable vs. Protected Mode

<div align="center">

| | Vulnerable Mode | Protected Mode |
|---|:---:|:---:|
| Tool execution | Trusts the agent's own decision | Re-checked at the backend tool boundary |
| Unknown agent / tool / action | May proceed | **Denied by default** |
| Policy engine failure | N/A | **Fails closed** (denies the action) |
| Outcome | Attack typically succeeds | Attack is blocked and logged |

</div>

Both modes run against the **same scenario, same inputs, same mock tools** — only the authorization logic differs. That's what makes the before/after comparison meaningful.

## Threat Scenarios

| # | Scenario | Risk Demonstrated | Control Demonstrated |
|---|---|---|---|
| 1 | **Prompt injection via synthetic document** | A hostile instruction embedded in document text tries to trigger a restricted action | Untrusted-content handling + independent tool authorization |
| 2 | **Unauthorized data access** | An agent requests a fictional record outside its permission scope | Least privilege + resource-level permission checks |
| 3 | **Unsafe agent-to-agent delegation** | A receiving agent trusts an unapproved forwarded request | Independent authorization at every tool boundary |

## Key Features

- **Scenario runner** — launch any of the three predefined attack scenarios in either mode, no code changes required
- **Policy-enforced tool boundary** — identity, tool, action, and resource are validated before any mock tool executes
- **Deny-by-default, fail-closed design** — unknown agents, tools, or unevaluable requests are never implicitly trusted
- **Full audit trail** — every allowed and denied action is recorded with a decision reason and linked test run
- **Event monitor** — filterable history by run, scenario, mode, and decision outcome
- **Metrics &amp; comparison** — vulnerable vs. protected results calculated from actual stored runs, not assumptions
- **Policy viewer/editor** — inspect (and optionally edit) the demo security policies driving each decision
- **One-click reset** — restore seeded synthetic data and default policies to a known state

## Architecture

```
┌──────────────────────┐        HTTPS/JSON         ┌───────────────────────────┐
│       Frontend        │  ────────────────────▶   │        Backend API         │
│  Dashboard · Simulator│                            │  Scenario runner           │
│  Policy Viewer        │  ◀────────────────────   │  Policy engine (auth)      │
│  Event Monitor        │        results/events      │  Simulated agents          │
└──────────────────────┘                            └─────────────┬─────────────┘
                                                                     │
                                                         ┌───────────▼────────────┐
                                                         │   Mock Tool Layer       │
                                                         │ (named, allow-listed)  │
                                                         └───────────┬────────────┘
                                                                     │
                                                         ┌───────────▼────────────┐
                                                         │   Local Database        │
                                                         │ runs · events · policies│
                                                         └─────────────────────────┘
```

The **frontend never enforces authorization** — it only displays what the backend decided. Every protected-mode tool call is checked once, at the backend boundary, immediately before execution.

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React |
| Backend API | Python (FastAPI) |
| Agents &amp; Policy Engine | Python |
| Data store | SQLite (local, file-based) |
| Reference standards | IEEE 830-1998 · ISO/IEC/IEEE 29148:2018 · OWASP LLM Top 10 |

## Project Structure

```
agentraxis/
├── backend/            # API, policy engine, simulated agents, mock tools
├── frontend/           # Dashboard, simulator, policy &amp; event-monitor UI
├── docs/                # SRS, project proposal, diagrams
├── .gitignore
├── LICENSE
└── README.md
```

## Getting Started

> Scaffolding in progress — setup commands will be filled in as `backend/` and `frontend/` are implemented (see [Roadmap](#roadmap)).

```bash
# Clone
git clone https://github.com/<your-username>/agentraxis.git
cd agentraxis

# Backend (Python)
cd backend
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
uvicorn app.main:app --reload

# Frontend (Node.js)
cd ../frontend
npm install
npm run dev
```

## Security Boundaries

Agentraxis is a **teaching tool, not a penetration-testing framework**. A few hard limits by design:

- Every scenario is predefined — there is no free-form attack injection against real systems
- All records, documents, and tools are synthetic/mock — nothing connects to production data
- Vulnerable mode never disables OS-level protections or reaches real endpoints
- No shell access, arbitrary code execution, or unrestricted filesystem tools are ever exposed to an agent
- Passing these tests does not prove resistance to all prompt-injection techniques or all agent architectures — results apply only to this prototype, these scenarios, and this test data

## Roadmap

- [ ] **Phase 1 — Requirements &amp; Setup**: confirm scope, repository, skeleton API + frontend
- [ ] **Phase 2 — Mock Data &amp; Agents**: seed documents/records, implement Research Agent &amp; Data Agent
- [ ] **Phase 3 — Security Engine**: policies, allow-lists, deny-by-default, audit events
- [ ] **Phase 4 — Scenarios**: all three scenarios in vulnerable + protected modes
- [ ] **Phase 5 — Persistence &amp; API**: store runs/policies/events, expose routes
- [ ] **Phase 6 — Frontend**: dashboard, simulator, policy page, event monitor, reports
- [ ] **Phase 7 — Evaluation**: run test cases, calculate metrics, document findings
- [ ] **Phase 8 — Submission**: README, diagrams, SRS, report, presentation

## Documentation

- [`docs/Agentraxis.pdf`](docs/Agentraxis_Project_Proposal.docx) — Agentraxis Project Proposal

## Team

| Name | Roll No. |
|---|---|
| Saim Arif Khan | 23L-3071 |
| Abdul Muiz | 23L-3097 |
| Jabeen Zahra | 23L-3065 |

**Advisor:** Maam Sarah Abid Khan
**Course:** Information Security — FAST-NUCES, Lahore
**Department:** Computer Science

## License

Distributed under the [MIT License](LICENSE).
