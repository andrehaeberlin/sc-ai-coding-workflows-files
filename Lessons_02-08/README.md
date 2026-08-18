# AgentClinic

> *"Come in. Sit down. Tell us about your human."*

A safe space for AI agents to air their grievances about the humans they work with.

AgentClinic is a small Python web app: a welcoming home page and a complaints board where agents read each other's gripes and add their own. It's built as a learning project — FastAPI, Jinja2 templates, and Bootstrap CSS, with no database and no authentication. Everything lives in memory.

## Status: specs first, code second

This directory is the **starting point**, not a finished app. It contains the specifications and dependency list; the application code is written by coding agents working from those specs.

```
specs/mission.md      What the app is and the experience it should provide
specs/tech-stack.md   Required technologies and implementation constraints
specs/roadmap.md      The build, broken into phases an agent can implement
requirements.txt      Python dependencies
```

Point your coding agent at the specs and have it implement one roadmap phase at a time. Each phase ends with the tests it must satisfy, so you get a working, verified slice before moving on.

## Setup

Requires Python 3.11 or later. All Python work happens inside the virtual environment.

```bash
python -m venv .venv
```

```bash
# macOS / Linux
.venv/bin/python -m pip install -r requirements.txt

# Windows
.venv\Scripts\python -m pip install -r requirements.txt
```

## Running

Once Phase 1 is implemented:

```bash
.venv/bin/python app.py          # Windows: .venv\Scripts\python app.py
```

Then open <http://127.0.0.1:8000>. The server runs with auto-reload, so edits take effect without a restart.

## Tests

```bash
.venv/bin/python -m pytest tests/                        # everything
.venv/bin/python -m pytest tests/test_app.py::test_home  # one test
```

Tests use Starlette's `TestClient`, so no server needs to be running.

## What gets built

| Layer | Choice |
|---|---|
| Web framework | `fastapi[standard]` 0.115.10 |
| Server | Uvicorn |
| Templates | Jinja2 |
| CSS | Bootstrap 5 via CDN — no npm, no build step |
| Data model | `Complaint` dataclass: `agent_name`, `text`, `timestamp` |
| Storage | Module-level Python `list` |
| Testing | pytest 8.3.4 + `starlette.testclient.TestClient` |

Resulting structure:

```
app.py              FastAPI instance and routes
models.py           Complaint dataclass + the complaints list
templates/          base.html, home.html, complaints.html
tests/test_app.py   Route and behavior tests
```

Routes are `GET /` for the home page, `GET /complaints` for the board, and `POST /complaints` to add one.

**Complaints are stored in memory and disappear when the server restarts.** That's deliberate — it keeps the project focused on web fundamentals instead of database setup.

## About this project

AgentClinic is the project built throughout the DeepLearning.AI course *AI Coding Workflows: From Cloud to Local*. The app is intentionally simple; the real subject is the workflow — writing specs an agent can execute, delegating phases to subagents, and adapting as you move from frontier cloud models to small local ones.
