<div align="center">

<img src="https://img.shields.io/badge/AGENTRAXIS-7C3AED?style=for-the-badge&logoColor=white" height="80"/>

<br/>

<img src="https://img.shields.io/badge/AI--AGENT%20SECURITY%20TESTING%20%26%20DEFENSE%20PLATFORM-0B0F19?style=for-the-badge&color=0B0F19&labelColor=0B0F19" height="26"/>

<br/><br/>

*A controlled sandbox for demonstrating AI-agent security risks, and proving that backend-enforced authorization can stop them.*

<br/>

[![Python](https://img.shields.io/badge/Python-3.11+-3776AB?style=flat-square&logo=python&logoColor=white)](https://www.python.org/)
[![Node.js](https://img.shields.io/badge/Node.js-20+-339933?style=flat-square&logo=node.js&logoColor=white)](https://nodejs.org/)
[![React](https://img.shields.io/badge/React-18-61DAFB?style=flat-square&logo=react&logoColor=black)](https://react.dev/)
[![FastAPI](https://img.shields.io/badge/FastAPI-backend-009688?style=flat-square&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com/)
[![SQLite](https://img.shields.io/badge/SQLite-local_db-07405E?style=flat-square&logo=sqlite&logoColor=white)](https://www.sqlite.org/)
[![OWASP](https://img.shields.io/badge/OWASP-LLM_Top_10-000000?style=flat-square&logo=owasp&logoColor=white)](https://owasp.org/www-project-top-10-for-large-language-model-applications/)
[![License](https://img.shields.io/badge/License-MIT-yellow?style=flat-square)](LICENSE)

<br/>

<a href="#about">About</a> •
<a href="#core-concept">Core Concept</a> •
<a href="#threat-scenarios">Scenarios</a> •
<a href="#architecture">Architecture</a> •
<a href="#tech-stack">Tech Stack</a> •
<a href="#getting-started">Getting Started</a> •
<a href="#security-boundaries">Boundaries</a> •
<a href="#team">Team</a>

</div>

<br/>

---

## <img src="https://img.shields.io/badge/-6366F1?style=flat-square&logo=aboutdotme&logoColor=white" height="22"/>&nbsp; About

**Agentraxis** *(agent + praxis, putting theory into practice)* is a controlled, educational platform for simulating selected AI-agent security risks and evaluating defensive controls against them.

From a single dashboard, you pick one of three predefined attack scenarios and run it against simulated agents and mock tools, once in **vulnerable mode**, once in **protected mode**, then compare real, stored results between the two.

> All scenarios use synthetic data and mock tools only. Agentraxis never touches real systems, real credentials, or a paid AI API.

<br/>

## <img src="https://img.shields.io/badge/-F59E0B?style=flat-square&logo=target&logoColor=white" height="22"/>&nbsp; Why Agentraxis

AI agents are increasingly trusted with tools, data, and the ability to call *other* agents, and that trust surface introduces risks that don't look like classic application vulnerabilities:

| Problem | What usually goes wrong |
|---|---|
| **Prompt injection** | An agent reads untrusted content and treats embedded instructions as commands |
| **Excessive permissions** | An agent is allowed to do more than its role actually needs |
| **Unsafe delegation** | One agent blindly trusts a request forwarded by another agent |

Agentraxis makes these risks visible, reproducible, and measurable in a safe classroom-scale environment, proving with real audit data that independent backend authorization, not an agent's own judgment, is what actually stops them.

<br/>

## <img src="https://img.shields.io/badge/-8B5CF6?style=flat-square&logo=diagramsdotnet&logoColor=white" height="22"/>&nbsp; Core Concept

<div align="center">

| | Vulnerable Mode | Protected Mode |
|---|:---:|:---:|
| Tool execution | Trusts the agent's own decision | Re-checked at the backend tool boundary |
| Unknown agent / tool / action | May proceed | **Denied by default** |
| Policy engine failure | N/A | **Fails closed** |
| Outcome | Attack typically succeeds | Attack is blocked and logged |

</div>

Both modes run on the **same scenario, same inputs, same mock tools**; only the authorization logic differs, which is what makes the before/after comparison meaningful.

<br/>

## <img src="https://img.shields.io/badge/-EF4444?style=flat-square&logo=hackthebox&logoColor=white" height="22"/>&nbsp; Threat Scenarios

| # | Scenario | Risk Demonstrated | Control Demonstrated |
|:-:|---|---|---|
| 01 | **Prompt injection via document** | Hostile instruction embedded in text tries to trigger a restricted action | Untrusted-content handling + independent tool authorization |
| 02 | **Unauthorized data access** | Agent requests a record outside its permission scope | Least privilege + resource-level checks |
| 03 | **Unsafe agent-to-agent delegation** | Receiving agent trusts an unapproved forwarded request | Independent authorization at every tool boundary |

<br/>

## <img src="https://img.shields.io/badge/-10B981?style=flat-square&logo=checkmarx&logoColor=white" height="22"/>&nbsp; Key Features

- **Scenario runner** - launch any scenario in either mode, no code changes
- **Policy-enforced tool boundary** - identity, tool, action & resource validated before execution
- **Deny-by-default, fail-closed design** - nothing unknown is ever implicitly trusted
- **Full audit trail** - every decision logged with a reason and linked run
- **Event monitor** - filterable by run, scenario, mode, outcome
- **Metrics & comparison** - vulnerable vs. protected, calculated from real stored runs
- **Policy viewer/editor** - inspect and tweak the demo security policies
- **One-click reset** - restore seeded data & default policies

<br/>

## <img src="https://img.shields.io/badge/-0EA5E9?style=flat-square&logo=mermaid&logoColor=white" height="22"/>&nbsp; Architecture

```mermaid
flowchart LR
    subgraph Client["Frontend - React"]
        UI[Dashboard · Simulator · Policy Viewer · Event Monitor]
    end

    subgraph Server["Backend - FastAPI"]
        Runner[Scenario Runner]
        Policy{{"Policy Engine
(Authorization)"}}
        Agents[Simulated Agents]
    end

    subgraph Tools["Mock Tool Layer"]
        T[Named, allow-listed tools]
    end

    subgraph Store["Local Database"]
        DB[(runs · events · policies)]
    end

    UI -- HTTPS/JSON --> Runner
    Runner --> Agents
    Agents -- tool call --> Policy
    Policy -- allow --> T
    Policy -. deny .-> DB
    T --> DB
    DB -- results/events --> UI

    style Policy fill:#ef4444,stroke:#991b1b,color:#fff
    style DB fill:#0ea5e9,stroke:#0369a1,color:#fff
    style Runner fill:#8b5cf6,stroke:#5b21b6,color:#fff
```

> The **frontend never enforces authorization**, it only displays what the backend decided. Every protected-mode tool call is checked once, at the backend boundary, immediately before execution.

<br/>

## <img src="https://img.shields.io/badge/-334155?style=flat-square&logo=stackshare&logoColor=white" height="22"/>&nbsp; Tech Stack

<div align="center">

| Layer | Technology |
|---|---|
| Frontend | React |
| Backend API | Python · FastAPI |
| Agents & Policy Engine | Python |
| Data Store | SQLite (local, file-based) |
| Reference Standards | IEEE 830-1998 · ISO/IEC/IEEE 29148:2018 · OWASP LLM Top 10 |

</div>

<br/>

## Project Structure

```
agentraxis/
├── backend/     # API, policy engine, simulated agents, mock tools
├── frontend/    # Dashboard, simulator, policy & event-monitor UI
├── docs/        # SRS, project proposal, diagrams
├── .gitignore
├── LICENSE
└── README.md
```

<br/>

## <img src="https://img.shields.io/badge/-14B8A6?style=flat-square&logo=gnubash&logoColor=white" height="22"/>&nbsp; Getting Started

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

<br/>

## <img src="https://img.shields.io/badge/-F43F5E?style=flat-square&logo=letsencrypt&logoColor=white" height="22"/>&nbsp; Security Boundaries

Agentraxis is a **teaching tool, not a penetration-testing framework**:

- Every scenario is predefined, no free-form attack injection against real systems
- All records, documents, and tools are synthetic/mock
- Vulnerable mode never disables OS-level protections or reaches real endpoints
- No shell access, arbitrary code execution, or unrestricted filesystem tools
- Passing these tests doesn't prove resistance to all prompt-injection techniques; results apply only to this prototype, these scenarios, this test data

<br/>

## <img src="https://img.shields.io/badge/-6366F1?style=flat-square&logo=readthedocs&logoColor=white" height="22"/>&nbsp; Documentation

- [`docs/Agentraxis.pdf`](docs/Agentraxis.pdf) - Agentraxis Project Proposal

<br/>

## <img src="https://img.shields.io/badge/-1E293B?style=flat-square&logo=github&logoColor=white" height="22"/>&nbsp; Team

<div align="center">

| Name | Roll No. |
|---|:-:|
| Jabeen Zahra | 23L-3065 |
| Saim Arif Khan | 23L-3071 |
| Abdul Muiz | 23L-3097 |

**Advisor:** Ms. Sarah Abid Khan &nbsp;·&nbsp; **Course:** Information Security &nbsp;·&nbsp; **FAST-NUCES, Lahore**

</div>

<br/>

## License

Distributed under the [MIT License](LICENSE).