# Rights Angel — Milestones 1 & 2

**Version:** M1 + M2 Complete  
**Stack:** Python 3.11+ · FastAPI · SQLite · Jinja2 · OpenAI GPT-4o  
**Language:** Hebrew (RTL UI) · English (API + code)

---

## Prerequisites

- Python 3.11 or higher
- pip
- An OpenAI API key (GPT-4o access)

---

## Setup Instructions

### Step 1 — Clone / Extract

If you received a zip file, extract it:
```
unzip rights_angel_m1_m2.zip
cd right_rag-main
```

### Step 2 — Create virtual environment (recommended)
```
python3 -m venv venv
source venv/bin/activate        # Mac / Linux
venv\Scripts\activate           # Windows
```

### Step 3 — Install dependencies
```
pip install -r requirements.txt
```

### Step 4 — Create .env file

Create a file named `.env` in the root folder (same level as `main.py`):
```
OPENAI_API_KEY=sk-your-actual-key-here
OPENAI_MODEL=gpt-4o
DB_PATH=rights_angel.db
RESET_SECRET=choose-a-secret-string
```

**Important:** Replace `sk-your-actual-key-here` with your real OpenAI API key.

### Step 5 — Run the server
```
python main.py
```

The server starts at: **http://localhost:8000**

---

## Screen Reference

| URL | Screen | Purpose |
|-----|--------|---------|
| /screen0 | Home | Rights catalog overview |
| /screen1 | Upload | Upload legal documents (any domain, Hebrew or English ID) |
| /screen2 | Decomposition | Review extracted clauses, approve / reject |
| /screen3 | Engine Draft | View rule engine draft |
| /screen4 | Validation | Self-validation report + contradiction detection |
| /screen5 | Explainability | Admin explainability view |
| /screen6 | Expert Approval | Human expert approval gate for clauses |
| /screen7 | Calculator | Arnona discount calculator |
| /screen8 | Appeal | Appeal information |
| /screen9 | Audit | Audit log + engine version history |
| /screen10 | Fact Collector | Citizen fact collection (L4) |
| /screen11 | Result | Eligibility result with Hebrew explanation |
| /screen12 | Pipeline | Visual pipeline diagram |
| /screen13 | Legal Dictionary | Enriched clause view — matches file 01 format |
| /screen14 | Expert Questions | Auto-generated expert questions — matches file 04 format |

---

## Standard Workflow

```
Upload document (screen1)
        ↓
Review + approve clauses (screen2 → screen6)
        ↓
View legal dictionary (screen13)
        ↓
Generate + answer expert questions (screen14)
        ↓
Citizen fills facts (screen10)
        ↓
Evaluate eligibility (screen11)
        ↓
Check audit log (screen9)
```

---

## Key Features

**M1 — Generic Ingestion + Decomposition**
- Any legal domain accepted (arnona, immigration, education, welfare, etc.)
- Hebrew or English document IDs supported (e.g. `עולים-2026` or `GOV-IL-OLIM-2026`)
- GPT-4o extracts atomic clauses with plain explanation, evidence required, approving authority, confidence level
- Contradiction detection across clauses
- Human review gate — no clause enters the engine without expert approval

**M2 — Never Go Silent**
- Every evaluation returns one of four explicit states:
  - `ELIGIBLE` — evidence found, result issued
  - `INELIGIBLE` — conditions not met
  - `INSUFFICIENT_EVIDENCE` — required facts missing, with list of what is missing
  - `DOMAIN_NOT_INGESTED` — legal domain not yet in system, with list of supported domains
- Upload failures surface with specific reason codes (not silent "0 clauses")
- Legal dictionary view (screen13) — plain explanation, evidence needed, approving authority per clause
- Expert questions view (screen14) — auto-generated ambiguities for human legal expert review

---

## API Documentation

Interactive API docs available at: **http://localhost:8000/docs**

Key endpoints:
```
POST /api/ingest                          Upload a legal document
GET  /api/documents/status                List documents with ingestion status
GET  /api/documents/{doc_id}/summary      Post-upload summary
GET  /api/review/pending                  Clauses awaiting review
POST /api/review/approve                  Approve a clause
POST /api/review/reject                   Reject a clause
GET  /api/clauses                         Query clause store
POST /api/session/start                   Start citizen session
POST /api/facts                           Submit citizen facts
POST /api/evaluate/{session_id}           Run eligibility evaluation
GET  /api/expert-questions                List all expert questions
POST /api/documents/{id}/generate-expert-questions  Auto-generate expert questions
POST /api/expert-questions/{id}/answer    Record expert answer
GET  /api/validate                        Run integrity validation
GET  /api/audit                           Audit log
```

---

## Environment Variables

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| OPENAI_API_KEY | Yes | — | Your OpenAI API key |
| OPENAI_MODEL | No | gpt-4o | OpenAI model to use |
| DB_PATH | No | rights_angel.db | SQLite database file path |
| RESET_SECRET | No | — | Secret for /api/reset-db-temp (dev only) |

---

## Troubleshooting

**"0 clauses extracted" after upload**
→ Check your OPENAI_API_KEY in .env
→ Check the document is not empty or image-only PDF
→ View reason in screen2 top alert or screen9 audit log

**"Failed to fetch" on upload**
→ Make sure server is running (`python main.py`)
→ Check browser console for network errors

**Hebrew document ID rejected**
→ Update to latest version — Hebrew IDs (e.g. `עולים-2026`) are now supported

**Database reset (development only)**
→ `GET /api/reset-db-temp?secret=YOUR_RESET_SECRET`

---

## Project Structure

```
right_rag-main/
├── main.py                    FastAPI app + screen routes
├── requirements.txt           Python dependencies
├── .env                       Your config (create this — not included)
├── api/
│   └── routes.py              All API endpoints
├── database/
│   └── schema.py              SQLite schema + migrations
├── engine/
│   ├── rule_engine.py         L5 eligibility evaluation
│   ├── decision_serializer.py L6 result serialization + Hebrew output
│   ├── fact_normalizer.py     L4 fact validation
│   ├── rights_catalog.py      Rights catalog seeding
│   └── version_manager.py     Engine version management
├── ingestion/
│   └── pipeline.py            L1+L2 document ingestion + clause extraction
└── templates/
    └── screen*.html           Admin + citizen UI screens (screen0–screen14)
```

---

## Notes

- The database (`rights_angel.db`) is created automatically on first run
- The rights catalog is seeded automatically on first run
- No data is included — the system starts empty and requires document upload
- Milestone 3 (Confidence Scoring + HITL) is delivered separately

---

*Rights Angel — Milestones 1 & 2 | August 2026*
