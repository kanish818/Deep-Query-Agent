# Deep Query Agent

Deep Query Agent lets you ask questions in plain English and get precise answers computed from **CSV files** or **SQL databases**.  
It translates natural language into safe SQL (or pandas operations), executes the plan, and returns both the **answer** and the **generated query** for transparency.

---

## Features
- **Natural language → SQL/DataFrame** (read-only by default)
- Works with **CSV** (pandas) and **SQLite/MySQL/Postgres** (via DB drivers)
- **Show your work**: optional `--show-sql` and `--dry-run`
- Handles **group-bys, filters, top-N, date ranges, aggregates**
- Basic **guardrails**: SELECT-only mode and parameterization where possible

---

## Repository Layout (example)
deep-query-agent/
├─ app2.py # CLI entry point (rename if needed)
├─ agent/ # Core agent logic
│ ├─ nlp.py # Model helpers & prompts
│ ├─ sql.py # SQL builder / sanitizer / validator
│ ├─ runners.py # CSV (pandas) & SQL runners
│ └─ schema.py # Schema discovery & table metadata
├─ data/ # Sample CSVs (optional)
├─ requirements.txt # Python dependencies
└─ README.md

## Requirements
- **Python 3.9+** (3.10/3.11 recommended)
- Packages from `requirements.txt`
- Optional: API key for a hosted LLM (if not using a local model)

### Environment Variables (set only what you use)
- `OPENAI_API_KEY` / `ANTHROPIC_API_KEY` / `GROQ_API_KEY`
- `MODEL_NAME` (e.g., `gpt-4o-mini`)
- `READ_ONLY=true` (recommended)
- `MAX_ROWS=10000`

---

## Installation
```bash
python -m venv .venv
# Windows PowerShell:
. .\.venv\Scripts\Activate.ps1
# macOS/Linux:
# source .venv/bin/activate

pip install -r requirements.txt

## Quick Start

A) Query a CSV
# Optional for hosted models:
# PowerShell:  $env:OPENAI_API_KEY="sk-..."
# bash/zsh:    export OPENAI_API_KEY="sk-..."

python app2.py --csv data/some.csv --ask "Top 5 products by revenue in 2024?"
python app2.py --csv data/some.csv --dry-run --ask "Average order value by region"

B) Query a SQLite database
python app2.py --sqlite path/to/db.sqlite --ask "Monthly active users by month in 2025?"

C) MySQL/Postgres (if implemented)
python app2.py --mysql --host localhost --port 3306 --user readonly --database analytics \
  --ask "Signups by country last quarter"

Common CLI Flags

--ask "<question>" : Natural-language question

--csv <path> : Query a CSV via pandas

--sqlite <path> : Query a SQLite database

--dry-run : Print generated SQL/plan only (no execution)

--show-sql : Always print final SQL

--limit <N> : Add row limit to the query

--schema a=path1.csv b=path2.csv : Register multiple CSVs

How It Works

Schema discovery from CSV headers or DB tables

Planning using an LLM or templates to form SQL/DataFrame ops

Validation: enforce read-only, parameterize literals, optional row caps

Execution via pandas or DB driver

Answer + Query returned together for trust & verification

Troubleshooting

DB errors: verify credentials, connectivity, and that the driver package is installed

CSV issues: check file paths and headers (avoid duplicate or messy column names)

Model confusion: simplify/standardize header names; enable --show-sql to inspect the query

Security Notes

Prefer read-only (SELECT-only) execution (READ_ONLY=true)

Parameterize literals where possible

Optionally restrict allowed schemas/tables in code

Roadmap

Multi-CSV join reasoning

Automatic chart suggestions

Simple web UI (Streamlit/Gradio)

Caching of repeated queries

License

MIT
