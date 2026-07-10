                                                           Bilingual Travel Operations Agent — English & Mandarin Lead Processing

A small internal travel-operations agent — **not** a customer-facing booking site — built to demonstrate multilingual workflow automation, structured AI extraction, API design, validation, database handling, and Docker deployment.

## Why This Project

When a Chinese or English travel enquiry comes in, this agent turns it into:

- A clean, structured operational record
- A list of any missing details
- A bilingual summary for internal staff
- A human-review flag when the enquiry needs manual attention

## Architecture
```
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
3. **Pydantic Request Validation** — validates incoming API request data and response schemas.
4. **SQLite Lead Database** — persists validated leads for downstream use.
5. **Missing Detail Detection** — checks the validated record against required fields and flags anything the enquiry left out.
6. **Human Review Routing** — A lead is routed for human review when essential travel details are missing or when the enquiry is marked urgent. This prevents incomplete or time-sensitive requests from being treated as ready-to-process travel bookings.
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



## Error Handling and Validation

If Gemini extraction fails because of an unavailable API key, network issue, rate limit, or invalid model response, the service returns a safe default extraction result instead of failing the API request.

The workflow then identifies missing travel details and routes the enquiry for human review. This keeps the service operational when the LLM provider is temporarily unavailable.

FastAPI and Pydantic validate incoming request data before processing. Each enquiry must contain a valid `message` field. Invalid requests return FastAPI’s standard `422 Validation Error` response.

## Current API Endpoints

| Method | Endpoint         | Purpose                                                                                                                                    |
| ------ | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| `POST` | `/leads/process` | Processes an English or Mandarin travel enquiry, extracts details with Gemini, saves the lead, and returns a bilingual operations summary. |
| `GET`  | `/leads`         | Returns saved travel leads from the SQLite database.                                                                                       |
| `GET`  | `/docs`          | Opens the interactive Swagger API documentation.                                                                                           |

## Planned Enhancements

* CSV export for processed travel leads
* Lightweight operations dashboard
* Persistent database volume for Docker deployment
* Expanded automated test coverage


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

* Mandarin customer enquiry handling
* Gemini-based information extraction
* Pydantic request and response validation
* SQLite database storage
* Missing-detail detection and human-review routing
* Bilingual internal staff summary
* Docker deployment
* Swagger API documentation at `/docs`
* Travel lead automation
* Structured data processing
* Backend API development
* Database workflow


## Running Tests

```bash
pytest tests/
```

## License

No license specified — personal/portfolio project.
