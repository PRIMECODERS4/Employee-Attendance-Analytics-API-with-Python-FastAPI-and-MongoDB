# Employee Attendance & Analytics API

FastAPI + MongoDB implementation of the HROne engineering assignment. The HTTP
contract and business rules R1–R10 live in `openapi.yaml`. The MongoDB data
model (two collections, field types, sample documents) lives in `DATA_MODEL.md`.
All application code is in `app/main.py`.

## Run locally

Requires Python 3.11+ and MongoDB 6.0+. From the project root:

```bash
python -m venv .venv
# macOS / Linux
source .venv/bin/activate
# Windows PowerShell
.\.venv\Scripts\Activate.ps1

pip install -r requirements.txt

# Start MongoDB (local Docker example).
docker run --name attendance-mongo -p 27017:27017 -d mongo:7

# Seed the nine sample documents from sample_data/ into the database.
python sample_seed.py

# Start the API.
uvicorn app.main:app --port 8000
```

The app reads `MONGO_URI` and `MONGO_DB` from environment variables. A local
`.env` file is fine, but real environment variables win. The default is
`mongodb://localhost:27017` and database `attendance_db`.

The service creates all its indexes on startup (idempotently), so the grader
can start it against a database that already holds the hidden dataset.

Interactive API docs are at `/docs`; the raw OpenAPI JSON is at `/openapi.json`.

## Project layout

```
app/main.py             All FastAPI code: routes, helpers, validation, analytics pipelines.
sample_data/            Nine sample documents (DATA_MODEL.md) as MongoDB Extended JSON.
sample_seed.py           Loads sample_data/ into the database (deletes existing first).
openapi.yaml             HTTP contract and business rules R1-R10.
DATA_MODEL.md            What is stored in MongoDB (collections, fields, samples).
REVIEW.md                Defects found in the starter and how they were fixed.
DECISIONS.md             Answers to the five design questions.
requirements.txt        Python dependencies.
tests/test_helpers.py    Unit tests for the pure helpers (R1-R7) — no MongoDB needed.
tests/test_integration.py End-to-end tests of every endpoint using mongomock.
```

## Tests

```bash
pip install pytest mongomock httpx
pytest -q
```

The unit tests (R1–R7 helpers) need no MongoDB. The integration tests use
`mongomock` so they run without a server, but they do not exercise the
aggregation operators that mongomock doesn't support (`$densify`,
`$setWindowFields`, `$facet`). For a full exercise, run against a real MongoDB
and use the seed data.

## Submission checklist

- `uvicorn app.main:app --port 8000` starts from a clean clone after
  `pip install -r requirements.txt`.
- All indexes are created at startup; no manual steps.
- `MONGO_URI` and `MONGO_DB` are read from the environment; nothing is
  hard-coded.
- `REVIEW.md` and `DECISIONS.md` are filled in.
- No `.env`, secrets, Dockerfile or virtual environment are committed.
