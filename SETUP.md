# Setup

## Prerequisites

- Python 3.10+
- Go
- Git

## Install

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

## Configure

```bash
cp .env.example .env
```

In `.env`, set:

- `CURSOR_API_KEY` — your Cursor API key (from the Cursor dashboard in your browser)
- `GITHUB_ISSUE_URL` — GitHub issue to fix

The target repo is **cloned automatically** into `test_repo/<repo>/` from the issue URL. You do not need `GITHUB_REPO_PATH` unless you want to override the path.

## Run

```bash
python main.py
```

The live dashboard opens in your browser automatically. To disable: `python main.py --no-ui` or `DASHBOARD_UI=false` in `.env`.

Outputs: `output/`. Logs: `logs/run_report.md`.
