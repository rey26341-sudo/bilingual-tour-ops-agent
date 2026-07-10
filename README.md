Bilingual AI Workflow Agent for Tour Operations
                    Build this as a small internal travel-operations agent, not a customer booking website.it demonstrates multilingual workflow automation, structured AI extraction, API design, validation, database handling, and Docker deployment.                                     
WHY I MADE THIS PROJECT: 
                    if Chinese or English travel enquiry made, then this agent returns a clean operational record, missing details, a bilingual summary, and a human-review flag.
                    

*PROJECT ARCHITECTURE:                                      

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

              FOLDER STRUCTURE:
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
├── requirements.txt
├── Dockerfile
├── docker-compose.yml
├── .env.example
├── README.md
└── sample_enquiries.json

*CREATED THE LOCAL PROJECT
CODES*******************************************
mkdir bilingual-tour-ops-agent
cd bilingual-tour-ops-agent

python3 -m venv venv
source venv/bin/activate
**************************************************

*CREATED DATABASE MODELS,
*SCRIPTED PYDANTIC SCHEMAS,
*ADDED LANGUAGE DETECTION,
*BUILT EXTRACTION SERVICE -python NLP techniques,
*WORKFLOW LOGIC,
*BILINGUAL SUMMARIES
*API ROUTES --FASTAPI APPLICATION
*TEST WITH CHINESE INPUT

CODES**************************************************************************
{
  "message": "客户想在8月15日去迪拜旅游，4个人，预算大约12000人民币，需要尽快回复。"
}
********************************************************************************
<img width="1920" height="1020" alt="Screenshot 2026-07-10 104506" src="https://github.com/user-attachments/assets/8f6b00db-059b-4e8b-9501-df865f497e74" />


<img width="1920" height="1020" alt="Screenshot 2026-07-10 105249" src="https://github.com/user-attachments/assets/dd0bb787-887c-4d1a-8346-e0dda3e111ec" />

<img width="1920" height="1020" alt="Screenshot 2026-07-10 105757" src="https://github.com/user-attachments/assets/b7203e86-83cd-4834-b9f0-74cbd0194e40" />

<img width="1920" height="1020" alt="Screenshot 2026-07-10 105916" src="https://github.com/user-attachments/assets/883666ef-c734-42d8-b6ba-82959b7177c0" />

<img width="1920" height="1020" alt="Screenshot 2026-07-10 112932" src="https://github.com/user-attachments/assets/4a45f634-69db-42da-8ddc-713667907651" />

*DOCKER FILE CREATED AND DOCKER COMPOSED- GOT ERROR BECAUSE OF 2 OR MANY RUNNING IN OLD CONTAINER..SO DEPLOYED docker-compose.yml  
****************
ports:
  - "8002:8000"
****************
<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/c1eeef07-1039-4904-919b-2e764f18c8d5" />

FINAL OUTPUT:
<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/94fe753f-4585-4199-8e6f-2df9aaf966c1" />

<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/b3ecb438-787c-441c-9920-8e231dd77d2b" />




THIS MANDARIN TEST CASE SOLVES:
mandarin customer enquiry handling
LLM-based information extraction
Pydantic validation
SQLite database storage
Human-review routing
CSV export
Bilingual internal staff summary
Docker deployment
Swagger API documentation at /docs
Travel lead automation
Structured data processing
Backend API development
Database workflow




