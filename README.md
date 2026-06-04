# Open Source Bug Resolver

Agent that reads a GitHub issue, understands the bug, plans and generates a fix, validates it with `go test`, and writes a PR summary — for approved Go projects (gin, cobra, validator, golangci-lint).

## Architecture

**Step 1 — Issue understanding**  
Fetches the issue and comments from GitHub and extracts identifiers, paths, and error strings.  
Optional LLM intake turns that into structured bug type, repro steps, and confidence.

**Step 2 — Context builder**  
Clones or uses the repo under `test_repo/<repo>/` and greps for relevant symbols and files.  
Slices only the candidate functions and repo conventions into a small context bundle.

**Step 3 — Fix plan**  
Cursor (or Gemini) reads the issue + context and writes a surgical fix plan in `output/plan.md`.  
Keeps scope tight: which files to change and what behavior to fix.

**Step 4 — Patch generation**  
The LLM produces a unified diff (`output/fix.patch`) aligned with the plan and Go style.  
Can retry with validation errors fed back into the prompt.

**Step 5 — Validation**  
Applies the patch with `git apply`, runs `go test ./...`, and retries on failure.  
Writes `output/validation_report.json` with apply and test results.

**Step 6 — PR summary**  
The LLM drafts title and body for a pull request in `output/pr_summary.md`.  
References the issue, plan, patch, and test outcome.

```
main.py → issue_understanding → context_builder → code_reasoning_agent
       → code_generator → validator → pr_writer
```

## Live demo

When you run `python main.py`, the **live HTML dashboard** starts at **http://127.0.0.1:8765/** and **opens automatically in your browser**.

You can watch each pipeline step, the diff, validation status, and logs update in real time. Disable with `--no-ui` or `DASHBOARD_UI=false` in `.env`.

## Setup

**Prerequisites:** Python 3.10+, Go, Git

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt

cp .env.example .env
```

In `.env`, set only:

- `CURSOR_API_KEY` — from the Cursor dashboard in your browser
- `GITHUB_ISSUE_URL` — full GitHub issue URL to fix

The target repo is cloned automatically into `test_repo/<repo>/`. No manual clone or `GITHUB_REPO_PATH` needed.

## Run

```bash
python main.py
```

**Outputs:** `output/` (`fix.patch`, `plan.md`, `pr_summary.md`)  
**Logs:** `logs/run_report.md`
