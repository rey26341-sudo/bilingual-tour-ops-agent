# Bilingual AI Workflow Agent for Tour Operations

A small internal travel-operations agent — **not** a customer-facing booking site — built to demonstrate multilingual workflow automation, structured AI extraction, API design, validation, database handling, and Docker deployment.

## Why This Project

When a Chinese or English travel enquiry comes in, this agent turns it into:

- A clean, structured operational record
- A list of any missing details
- A bilingual summary for internal staff
- A human-review flag when the enquiry needs manual attention

## Architecture

Chinese / English customer enquiry
              ↓
      Language Detection
              ↓
       Gemini LLM Extraction
              ↓
      Pydantic Validation
              ↓
        SQLite Lead Database
              ↓
    Missing Detail Detection
              ↓
    Human Review Routing
              ↓
 Bilingual Operations Summary
```

1. **Language Detection** — identifies whether the enquiry is in Chinese or English (and handles mixed-language input).
2. **Gemini LLM Extraction** — pulls structured fields (destination, dates, group size, budget, urgency, etc.) out of the raw enquiry text.
3. **Pydantic Validation** — validates the extracted data against a defined schema before it touches the database.
4. **SQLite Lead Database** — persists validated leads for downstream use.
5. **Missing Detail Detection** — checks the validated record against required fields and flags anything the enquiry left out.
6. **Human Review Routing** — leads with missing details or low-confidence extraction are marked for manual review rather than auto-processed. *(Adjust this description to match how your `workflow_service.py` actually flags/queues them — e.g. a `needs_review` column, a separate table, or a notification.)*
7. **Bilingual Operations Summary** — produces a staff-facing summary in both languages, covering what was extracted, what's missing, and whether it needs review.

## Folder Structure

```
bilingual-tour-ops-agent/
│
├── app/
│   ├── main.py
│   ├── models.py
│   ├── schemas.py
│   ├── database.py
│   ├── services/
│   │   ├── language_service.py
│   │   ├── extraction_service.py
│   │   ├── workflow_service.py
│   │   └── summary_service.py
│   └── routers/
│       └── leads.py
│
├── tests/
│   └── test_leads.py
│
├── docs/
│   └── architecture.png
│
├── requirements.txt
├── Dockerfile
├── docker-compose.yml
├── .env.example
├── README.md
└── sample_enquiries.json
```

## Tech Stack

- **FastAPI** — API framework and routing
- **Gemini LLM** — structured information extraction from free-text enquiries
- **Pydantic** — schema validation
- **SQLite** — lightweight lead database
- **Docker / docker-compose** — containerized deployment
- **Swagger UI** — interactive API docs at `/docs`

## Prerequisites

- Python 3.10+
- A Gemini API key
- Docker & docker-compose (optional, for containerized deployment)

## Getting Started

### Local Setup

```bash
mkdir bilingual-tour-ops-agent
cd bilingual-tour-ops-agent

python3 -m venv venv
source venv/bin/activate

pip install -r requirements.txt
```

Copy `.env.example` to `.env` and set your Gemini API key and any other required values before running the app:

```bash
cp .env.example .env
```

Run the API:

```bash
uvicorn app.main:app --reload
```

### Run with Docker

```bash
docker-compose up --build
```

The service is exposed on port `8002` (mapped to the container's `8000`):

```yaml
ports:
  - "8002:8000"
```

> If you hit an error about a container already running, stop or remove any older container bound to the same port before re-deploying:
> ```bash
> docker ps
> docker stop <container_id>
> ```

Once running, the interactive API docs are available at:

```
http://localhost:8002/docs
```

## Example Request

A Mandarin travel enquiry sent to the API:

```json
{
  "message": "客户想在8月15日去迪拜旅游，4个人，预算大约12000人民币，需要尽快回复。"
}
```

Roughly: *a customer wants to travel to Dubai on August 15th, a group of 4, with a budget of about ¥12,000, and needs a reply as soon as possible.*

The agent detects the language, extracts the structured fields (destination, date, group size, budget, urgency), validates them, stores the lead in SQLite, flags any missing details, and returns a bilingual operations summary.

## CSV Export

Validated leads can be exported to CSV for handoff to staff who work outside the API. *(Document the actual export endpoint or command here, e.g. `GET /leads/export` — update this section to match your implementation.)*

## Screenshots

**Development & testing**

<img width="1920" height="1020" alt="Screenshot 1" src="https://github.com/user-attachments/assets/8f6b00db-059b-4e8b-9501-df865f497e74" />

<img width="1920" height="1020" alt="Screenshot 2" src="https://github.com/user-attachments/assets/dd0bb787-887c-4d1a-8346-e0dda3e111ec" />

<img width="1920" height="1020" alt="Screenshot 3" src="https://github.com/user-attachments/assets/b7203e86-83cd-4834-b9f0-74cbd0194e40" />

<img width="1920" height="1020" alt="Screenshot 4" src="https://github.com/user-attachments/assets/883666ef-c734-42d8-b6ba-82959b7177c0" />

<img width="1920" height="1020" alt="Screenshot 5" src="https://github.com/user-attachments/assets/4a45f634-69db-42da-8ddc-713667907651" />

**Docker deployment**

<img width="1920" height="1020" alt="Docker deployment" src="https://github.com/user-attachments/assets/c1eeef07-1039-4904-919b-2e764f18c8d5" />

**Final output**

<img width="1920" height="1020" alt="Final output 1" src="https://github.com/user-attachments/assets/94fe753f-4585-4199-8e6f-2df9aaf966c1" />

<img width="1920" height="1020" alt="Final output 2" src="https://github.com/user-attachments/assets/b3ecb438-787c-441c-9920-8e231dd77d2b" />

## What This Mandarin Test Case Demonstrates

- Mandarin customer enquiry handling
- LLM-based information extraction
- Pydantic validation
- SQLite database storage
- Human-review routing
- CSV export
- Bilingual internal staff summary
- Docker deployment
- Swagger API documentation at `/docs`
- Travel lead automation
- Structured data processing
- Backend API development
- Database workflow

## Running Tests

```bash
pytest tests/
```

## License

No license specified — personal/portfolio project.
