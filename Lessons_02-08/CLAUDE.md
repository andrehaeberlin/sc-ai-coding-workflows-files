# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

Companion files for the DeepLearning.AI "AI Coding Workflows: From Cloud to Local" course. It contains **no application code** — only the specs, dependency list, and subagent configuration that a student uses to have coding agents build the app (AgentClinic) from scratch.

The app is the fixture, not the subject. The course holds one unchanging spec constant and walks it down a ladder of agent capability — frontier cloud model → cheaper cloud model → small local model — so the student sees how much delegation scaffolding each rung needs. Changes that make the app "better" but blur that progression are the wrong changes.

| Lessons | Tool | Model | Workflow taught |
|---|---|---|---|
| 2–3 | Claude Code | default | Specs first; implement phases in the main agent |
| 4–5 | Claude Code | general subagent, then Haiku | Delegate a phase per spawned subagent |
| 6–8 | OpenCode | DeepSeek v4 Pro/Flash via OpenRouter | Same workflow on a different harness |
| 9 | OpenCode | DeepSeek v4 Flash | Build a custom `implementer` subagent; delegate task-by-task |
| 10–12 | OpenCode | local LM Studio (Gemma, then Qwen) | Smaller phases, tight permissions, packet-style delegation |

The repo root holds three snapshot folders, each a complete copy of the project as it should look at that point:

| Folder | State |
|---|---|
| `Lessons_02-08` | Starting files: `specs/` + `requirements.txt` (this is the primary working directory) |
| `Lessons_09` | Adds `.opencode/agents/implementer.md` (DeepSeek v4 Flash via OpenRouter) |
| `Lessons_11-12` | Roadmap split into three phases + implementer switched to a local LM Studio model |

`specs/mission.md`, `specs/tech-stack.md`, and `requirements.txt` are byte-identical across all three folders, and `Lessons_09/specs/roadmap.md` matches `Lessons_02-08`'s. Only `roadmap.md` (2-phase vs 3-phase) and the presence/content of `.opencode/agents/implementer.md` differ. Keep the shared files in sync when editing.

`prompts.md` (repo root) is the authoritative source for every prompt used in the course, with per-lesson notes. Read it before changing any spec — specs and prompts are tightly coupled, and a spec edit can silently break the lesson that depends on it.

## The three specs

- **`mission.md`** — what AgentClinic is and why. Source of the tagline *"Come in. Sit down. Tell us about your human."*
- **`tech-stack.md`** — the binding constraint table: Python 3, `fastapi[standard]` 0.115.10, Uvicorn, Jinja2, Bootstrap 5 via CDN, `dataclasses.dataclass`, in-memory `list`, pytest 8.3.4 + `starlette.testclient.TestClient`. Ends with the venv requirement.
- **`roadmap.md`** — phases, each a task list ending in the tests it must satisfy. This is the unit of delegation.

Known discrepancy: `tech-stack.md` says Uvicorn is "run via `main.py`", while `roadmap.md` specifies `app.py` with `uvicorn.run("app:app", reload=True)`. The roadmap wins — agents building from these specs produce `app.py`.

## The app being built (AgentClinic)

FastAPI + Jinja2 + Bootstrap 5 (CDN), no database, no auth, no build step. Target shape once the roadmap is implemented:

```
app.py            # FastAPI instance, routes, uvicorn.run("app:app", reload=True) under __main__
models.py         # Complaint dataclass (agent_name, text, timestamp) + module-level `complaints: list`
templates/        # base.html, home.html, complaints.html
tests/test_app.py # pytest using starlette.testclient.TestClient (no running server)
```

Routes: `GET /` (home), `GET /complaints` (board), `POST /complaints` (add, then `RedirectResponse` status 303). State lives in a module-level list in `models.py` — restarting the server clears it. That is intentional, not a bug to fix.

Literals the roadmap's tests assert on — treat as contract, not decoration: the tagline above, the "Complaints Board" heading, the "AgentClinic" brand/title, seed complaints (3–5, generic agent gripes), and the `https://www.python.org/static/favicon.ico` favicon.

## Commands

Run from inside a lesson folder. `specs/tech-stack.md` requires the virtual environment for all Python work.

```powershell
python -m venv .venv
.venv\Scripts\python -m pip install -r requirements.txt
.venv\Scripts\python -m pytest tests/                          # all tests
.venv\Scripts\python -m pytest tests/test_app.py::test_name    # single test
.venv\Scripts\python app.py                                    # serve on http://127.0.0.1:8000
```

The course was recorded on macOS, so `prompts.md`, `specs/`, and `Lessons_11-12/.opencode/agents/implementer.md` all reference `.venv/bin/python`. On this Windows machine the equivalent is `.venv\Scripts\python`. Likewise the local-model IDs in those files are Mac MLX builds (`gemma-4-12b-it-mlx`, `qwen3.5-27b-ud-mlx`); Windows/Linux LM Studio uses GGUF IDs.

## Roadmap phases drive the work

Every course prompt asks an agent to implement one phase, and each phase ends with its validation tests. When editing the roadmap, keep phases self-contained and test-terminated — Lesson 11 exists precisely because Phase 2 was too large for a small local model, and splitting it (form work moved into a new Phase 3) is what made it fit.

## The implementer subagent

`.opencode/agents/implementer.md` is an **OpenCode** subagent (`mode: subagent` frontmatter), not a Claude Code one. The two versions demonstrate the course's central lesson — as model capability drops, the configuration must tighten:

- `Lessons_09`: cloud model, prose guidelines, unrestricted tools.
- `Lessons_11-12`: local model, `steps: 12` cap, `specs/*` edits denied, all bash denied except the single pytest command, `glob`/`webfetch`/`skill` disabled, and instructions that forbid `edit` in favor of writing complete files.

Preserve that contrast when modifying either file; flattening it removes the point of Lessons 9–12.

## Gotchas

- `Lessons_09` already contains the `implementer.md` that Lesson 9 teaches you to create. To follow that lesson live, delete it first and let the prompt regenerate it.
- `Lessons_11-12` already has the 3-phase roadmap split, so the Lesson 11 roadmap-split prompt should be skipped when starting from that folder.
- `.gitignore` excludes internal course material (`*Lesson Scripts*.md`, `scripts.md`) — this is a public repo; don't add authoring notes that would land in it.
- Toolchain the course assumes: Python 3.11+, Git, an IDE (PyCharm), Claude Code, OpenCode, an OpenRouter account, LM Studio.
