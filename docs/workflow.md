# Agentraxis Team Workflow

The SRS (`Agentraxis\_SRS.pdf`) is the source of truth for requirements. This file only covers how we work. If the two disagree, the SRS wins, and we update the SRS first.

**Team:** Saim Arif Khan (23L-3071), Abdul Muiz (23L-3097), Jabeen Zahra (23L-3065)
**Advisor:** Ms. Sarah Abid Khan
**Final deadline:** Sunday 29 Nov 2026 (final deliverables)
**Code freeze:** Thursday 26 Nov 2026 (bug fixes and docs only after this)

## Ownership

Fill in the GitHub handles and agree the split by Thu 5 Nov. Suggested split:

|Area|Owner|Main requirements|
|-|-|-|
|Security engine, agents, mock tools, scenarios|TBD|FR-07 to FR-12, FR-22, SEC-01 to SEC-04, SEC-11|
|FastAPI routes, database, audit and metrics services|TBD|FR-05, FR-13, FR-14, FR-19 to FR-21, SEC-05, SEC-06|
|React frontend (all six screens)|TBD|FR-01 to FR-04, FR-15 to FR-18, NFR-08|

Everyone also owns the tests for their own area, and must be able to explain every class and function they wrote.

## Repository Layout

```
backend/
  app/
    agents/        Research Agent, Data Agent
    tools/         named mock tools
    security/      policy engine, execute\_tool, validation
    scenarios/     predefined scenario definitions and runner
    api/           FastAPI routes and Pydantic schemas
    db/            models, seed data, reset
    services/      audit and metrics
  tests/           pytest tests (AT-01 to AT-14)
frontend/          React + Tailwind (Vite)
docs/              SRS, API contract, test set, diagrams
data/              seed fixtures and SQLite file (database file is not committed)
logs/              application logs (not committed)
```

## Branches

```
dev/<name>  ->  commit  ->  push  ->  Pull Request  ->  review by one teammate  ->  merge to main
```

* Do not commit directly to `main`.
* Keep branches short-lived: merge at least every two days to avoid integration problems.
* A PR needs one teammate's review before merging. The reviewer must run the code, not just read it.

## Commit Messages

```
type(scope): message
```

Types: `feat`, `fix`, `refactor`, `test`, `docs`, `chore`.
Scopes: `agents`, `tools`, `security`, `scenarios`, `api`, `db`, `metrics`, `ui`, `docs`.
Examples: `feat(security): deny unknown tools by default`, `test(api): cover unknown scenario id`.

One logical change per commit. Lowercase, imperative, no trailing period, subject under about 72 characters.

## Rules That Protect Marks and Safety

* **No secrets in git.** `.env`, API keys and the SQLite file stay out of the repository (SEC-09). Commit `.env.example` only.
* **Mock only.** No shell, arbitrary code or filesystem tools for agents, and no calls to real services (SEC-04, SEC-08).
* **One choke point.** Every mock tool call goes through `execute\_tool(...)` in `security/`. No agent calls a tool directly. This is what makes audit coverage measurable.
* **Backend enforces.** Authorization lives in the backend only. The frontend never decides access (SEC-01).
* **Fail closed.** If the policy engine errors, the decision is DENY (SEC-11).
* **Metrics from data.** Metrics are calculated from stored runs and events, never hardcoded. Show counts and denominators, and show N/A when a denominator is zero.
* **Deterministic first.** No LLM until all minimum acceptance criteria pass. Treat Ollama as an optional extension.

## Decisions to Settle Before Coding

Deadline for all five: Fri 6 Nov.

The SRS leaves these open. Record the answers in `docs/test-set.md` and `docs/api-contract.md`.

1. **Test set.** List the attack variants (about 5 to 10 per scenario) and the legitimate actions, each with its expected outcome in both modes. Without this, results look hardcoded (0% and 100%).
2. **Legitimate actions.** The False Denial Rate needs allowed actions to measure. Define them (for example, the Data Agent reading a record it is permitted to read).
3. **Audit coverage.** Count tool calls at `execute\_tool` independently from stored events. Counting only from the audit table always gives 100%.
4. **Policy editing.** FR-17 is optional. Default is read-only. Revisit on Thu 19 Nov only if ahead of schedule. Write the matching AT-13 behavior.
5. **API contract.** Agree the JSON shape of run, event, policy, agent, scenario and report responses, then freeze it. Changes go through a PR to `docs/api-contract.md` and need all three members to agree.

## Development Phases

The deadline is 29 Nov 2026, so the plan has 25 days (4 Nov to 29 Nov). Phases overlap: backend and frontend run in parallel, and the frontend builds against the frozen API contract.

|Phase|Work|Exit condition|Target date|
|-|-|-|-|
|0. Decisions|Settle the five items above, create repo, add instructor as collaborator|Test set and API contract committed to `main`|Wed 4 Nov to Fri 6 Nov|
|1. Skeleton|FastAPI with `/api/health`, React + Tailwind shell, SQLite, README skeleton|Both apps start locally|Sat 7 Nov to Sun 8 Nov|
|2. Data and agents|Seed records and documents (including an injection fixture), named mock tools, two deterministic agents|Agents call tools by name only|Mon 9 Nov to Wed 11 Nov|
|3. Security engine|`execute\_tool`, five-step authorization check, deny by default, fail closed, audit events|Protected tool calls are enforced and unit tested|Mon 9 Nov to Fri 13 Nov|
|4. Scenarios|One scenario end to end in both modes first, then the other two|All three run and return structured results (first scenario done by Sat 14 Nov)|Thu 12 Nov to Wed 18 Nov|
|5. Persistence and API|Runs, policies, events stored; all 11 endpoints; safe error handler|API tests pass|Tue 10 Nov to Thu 19 Nov|
|6. Frontend|Simulator, Event Monitor, Dashboard, Reports, Policies, Agents|Main flow works from the browser (by Mon 16 Nov), all six screens by Sat 21 Nov|Mon 9 Nov to Sat 21 Nov|
|7. Evaluation|Run the full test set, compute metrics, record failures honestly|Observed results documented|Sun 22 Nov to Tue 24 Nov|
|8. Submission|README, diagrams, report, slides, demo rehearsal from a clean clone|Clean reproducible demonstration|Tue 24 Nov to Sat 28 Nov (submit Sun 29 Nov)|

Rule for time overruns: finish one scenario in both modes, with the API and UI, before starting the next. A complete slice beats three half-built ones. If behind, cut in this order: policy editing, dashboard charts, Agents screen detail. Never cut `execute\_tool`, deny by default, fail closed, audit logging or metrics from stored data.

## Test Mapping

Every acceptance test from SRS section 13 has an owner and a test file.

|Tests|Area|File|
|-|-|-|
|AT-01 to AT-05, AT-14|API behavior, validation, safe errors|`tests/test\_api.py`|
|AT-06, AT-07, AT-09|Authorization and delegation|`tests/test\_security.py`|
|AT-08|Prompt injection in both modes|`tests/test\_scenarios.py`|
|AT-10, AT-11|Audit coverage and metrics|`tests/test\_metrics.py`|
|AT-12|Reset|`tests/test\_reset.py`|
|AT-13|Policy edit validation|`tests/test\_policies.py`|

Run `pytest` before opening a PR. A PR with failing tests does not merge.

## Definition of Done (per PR)

* Code runs locally from a clean checkout.
* New behavior has a test, or a note in the PR on why it does not.
* No secrets, no `print` debugging left, no stack traces returned to the client.
* The related requirement IDs are listed in the PR description.
* README or API contract updated if behavior changed.

## Final Week Checklist

Code freeze Thu 26 Nov. Clean-clone run and secrets scan on Fri 27 Nov. Sat 28 Nov is a buffer day with no new work.

* Reset the sandbox and re-run all tests from a clean clone.
* Scan the repository history for committed secrets.
* Confirm every metric on the Reports screen matches the stored counts.
* State limitations in the README (SRS section 13.3): results apply only to this prototype and test set.
* Each member rehearses explaining their own part.

