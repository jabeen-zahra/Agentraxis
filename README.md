<div align="center">

# ◆ Agentraxis

**AI-Agent Security Testing & Defense Platform**

*Simulate real AI-agent attacks. Prove that backend authorization stops them.*

<br/>

[![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com/)
[![React](https://img.shields.io/badge/React-61DAFB?style=for-the-badge&logo=react&logoColor=black)](https://react.dev/)
[![SQLite](https://img.shields.io/badge/SQLite-07405E?style=for-the-badge&logo=sqlite&logoColor=white)](https://www.sqlite.org/)
[![OWASP](https://img.shields.io/badge/OWASP_LLM_Top_10-000000?style=for-the-badge&logo=owasp&logoColor=white)](https://owasp.org/www-project-top-10-for-large-language-model-applications/)

</div>

<br/>

## ◆ What it is

Agentraxis is a sandbox where a tester launches one of three attack scenarios against simulated AI agents — once **unprotected**, once **protected** — and watches the backend authorization layer decide, in real time, what gets allowed and what gets denied. Every decision is logged. Nothing here touches a real system, a real record, or a paid AI API.

> **agent** + **praxis** — taking AI-agent security out of theory and into practice.

<br/>

## ◆ Vulnerable vs. Protected

<div align="center">

|  | Vulnerable Mode | Protected Mode |
|:--|:--:|:--:|
| Tool execution | trusts the agent | re-checked at the backend boundary |
| Unknown request | may proceed | **denied by default** |
| Engine failure | — | **fails closed** |
| Typical outcome | attack succeeds | attack blocked + logged |

</div>

<br/>

## ◆ Attack Scenarios

```
 1 ▸ Prompt Injection        hostile text in a document triggers a restricted action
 2 ▸ Unauthorized Access     an agent reaches for a record outside its permissions
 3 ▸ Unsafe Delegation       one agent blindly trusts a request forwarded by another
```

<br/>

## ◆ Architecture

```
                   ┌────────────────┐
                   │    Frontend    │   React — dashboard · simulator · event monitor
                   └────────────────┘
                           │
                           ▼
                   ┌────────────────┐
                   │   Backend API  │   FastAPI — scenario runner · policy engine
                   └────────────────┘
                           │
                           ▼
                   ┌────────────────┐
                   │   Mock Tools   │   named, allow-listed actions only
                   └────────────────┘
                           │
                           ▼
                   ┌────────────────┐
                   │     SQLite     │   runs · events · policies
                   └────────────────┘
```

The frontend never enforces authorization — it only renders what the backend decided.

<br/>

## ◆ Quick Start

```bash
git clone https://github.com/<your-username>/agentraxis.git && cd agentraxis

# backend
cd backend && python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt && uvicorn app.main:app --reload

# frontend
cd ../frontend && npm install && npm run dev
```

<br/>

## ◆ Team

<div align="center">

|                  |                |            |
|:-----------------|:---------------|:-----------|
| **Advisor**      | Maam Sarah Abid Khan | — |
| **Developer**    | Saim Arif Khan | `23L-3071` |
| **Developer**    | Abdul Muiz     | `23L-3097` |
| **Developer**    | Jabeen Zahra   | `23L-3065` |

**Information Security** · Department of Computer Science · FAST-NUCES, Lahore

</div>

<br/>

## ◆ License

[MIT](LICENSE)