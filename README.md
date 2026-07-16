  **Bilingual Travel Operations Agent — English & Mandarin Lead Processing**

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
<img width="1920" height="1020" alt="Screenshot 2026-07-16 105750" src="https://github.com/user-attachments/assets/3535d6da-c4a5-404d-a0c1-49f171e21d75" />
the following proof of screenshots explains 

✅ POST /documents/upload endpoint is working
✅ PDF validation works
<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/33f653e4-36cd-4c9c-824a-bf5559cf58b3" />

✅ PDF is uploaded successfully
✅ File is saved to documents/
<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/8cb9b357-089f-4456-8f0c-a8a40858e767" />

✅ FastAPI router is registered correctly
✅ Swagger UI is working
<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/856813c8-e015-456a-aa09-9415ba4fe6eb" />
FOUNDATION BLOCK OF RAG (Retrieval-Augumented Generation) system is implemented when
✅PDF uploaded successfully
✅ PyMuPDF opened the file
✅ Text extraction worked
✅ The text is ready for AI processing

<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/e838fce8-018c-416d-bc49-b1c927f12a0f" />

After this i can confirm that my pipeline works like this 

**PDF --upload API -- documents/ --PyMuPDF --Extract Text **

<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/0b09b057-d5b3-4f2c-80ed-d7254dee4eed" />

<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/34e15938-900e-4ddc-aebd-688aa6921a84" />

WHY OVERLAPPING CHUNKS! If information is split exactly at a chunk boundary, important context can be lost. By repeating a small portion of the previous chunk (the overlap), related information remains available in adjacent chunks, improving retrieval quality when performing semantic search. so  FastAPI,Lead Processing,PDF Upload,PDF Text Extraction,Document Chunking is implemented


**production-style RAG system**  --*GENERTAE EMBEDDINGS*
what is embeddings? computers don't understand languages
for example,
```
" i want to visit malaysia."
```
it is just a text to computer. we convert it into numbers:
```
[-0.23,
 0.88,
 0.17,
 ...
 0.04]
```
this numeric representation is called embeddings. similar meanings produce similar vectors.

Next:
⬜ Sentence Embeddings
⬜ Qdrant Vector DB
⬜ Semantic Search
⬜ Gemini RAG

--*SENTENCE TRANSFORMERS*
```
all-MiniLM-L6-v2
```
Why?

* Small (~90 MB)
* Fast
* Industry standard
* 384-dimensional vectors
* Excellent for learning RAG

---

# Step 1 — Install packages
```bash
pip install sentence-transformers
```
```bash
pip freeze > requirements.txt
```
# Step 2 — Create the embedding service
```bash
touch app/services/embedding_service.py
```
```bash
nano app/services/embedding_service.py
```
# Step 3 — Test embeddings
```bash
nano test_embedding.py
```
# Run it
```bash
python test_embedding.py
```
Each chunk becomes searchable by meaning instead of keywords. the output confirms this

<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/14b585de-12db-4faa-9ff3-9c81e4ba8b99" />

therefore every chunk now has its own vector.
✅ PDF Upload
✅ PDF Text Extraction
✅ Intelligent Chunking
✅ Sentence Transformer Embeddings

Why not store raw text?---Because raw text cannot be searched semantically. Embeddings let us compare meaning using vector similarity.
Why chunk before embedding?---Embedding an entire document loses precision and can exceed model limits. Chunking allows retrieval of only the relevant sections.
Why `all-MiniLM-L6-v2`?
* Lightweight
* Fast inference
* Strong retrieval performance
* 384-dimensional embeddings
* Commonly used in RAG tutorials and production prototypes

once the test succeeds, it was committed to github repository
# Commit
```bash
git add .
git commit -m "feat: add sentence-transformer embedding generation"
```

## **Qdrant**.

From now,Bilingual tour operations agent starts behaving like an enterprise knowledge assistant and the output confirms everything is working:

```text
Chunks: 4
Embeddings: 4
Embedding Dimension: 384

First 10 numbers:
[-0.1192, -0.0303, 0.0282, ...]
```

This means:

* ✅ PDF Upload
* ✅ PDF Text Extraction
* ✅ Intelligent Chunking
* ✅ Sentence Transformer Embeddings

This is no longer a CRUD API—it's becoming an AI application.

---

# Current Architecture

```text
                PDF
                 │
                 ▼
        /documents/upload
                 │
                 ▼
        documents/
                 │
                 ▼
      extract_text()
                 │
                 ▼
       chunk_text()
                 │
                 ▼
 generate_embeddings()
                 │
                 ▼
     384-d vectors ✅
```

The next missing component is the **Vector Database**.

---

# Integrate Qdrant

## What is Qdrant?

Right now vectors exist only in memory:

```python
embeddings = [
    [-0.12, 0.45, ...],
    [0.09, -0.34, ...]
]
```

If the application stops:

```text
Memory
   ↓
Lost ❌
```

A vector database stores them permanently and lets you search by similarity.

```text
Chunk
      ↓
Embedding
      ↓
Qdrant
      ↓
Similarity Search
```

# Why i chose Qdrant? --Qdrant is an open-source vector database optimized for semantic search. It stores embeddings efficiently and performs nearest-neighbor search using indexes such as HNSW, making retrieval fast even for millions of vectors.

# Install Qdrant Client

```bash
pip install qdrant-client
```

Then update:

```bash
pip freeze > requirements.txt
```

---

#Create the Vector Service

```bash
touch app/services/vector_service.py
```
```bash
nano app/services/vector_service.py
```

Replace the file with:

```python
from qdrant_client import QdrantClient
from qdrant_client.models import Distance, VectorParams

client = QdrantClient(path="vector_store")


COLLECTION_NAME = "tour_documents"


def create_collection():
    """
    Create the vector collection if it doesn't exist.
    """

    collections = client.get_collections().collections

    existing = [c.name for c in collections]

    if COLLECTION_NAME not in existing:

        client.create_collection(
            collection_name=COLLECTION_NAME,
            vectors_config=VectorParams(
                size=384,
                distance=Distance.COSINE
            )
        )

        print("Collection created.")

    else:

        print("Collection already exists.")
```

---

# Why `size=384`?

Because your embedding model:

```text
all-MiniLM-L6-v2
```

always produces:

```text
384 numbers
```

Every vector stored in this collection must have the same dimension.

---

# Step 3 — Test Collection Creation


```bash
nano test_qdrant.py
```
Run:
```bash
python test_qdrant.py
```

<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/3f7d82d8-520b-45ce-8be4-8fa2aa76eae4" />

This confirms Qdrant is initialized correctly. this project is using a real vector database.
✅ Qdrant installed correctly
✅ Local vector database created
✅ Collection created
✅ Vector dimension (384) is valid

```text
Exception ignored in:
ImportError: sys.meta_path is None, Python is likely shutting down
```
This happens because Python is exiting and the Qdrant client is trying to clean itself up during interpreter shutdown.this can be solved when FastAPI dependency injection or manage the client lifecycle more explicitly used in future cases.


# Store Embeddings --Once the collection is working, we'll extend `vector_service.py` with an `upsert_embeddings()` function that stores:

* the embedding vector,
* the original chunk text,
* document metadata (filename, chunk number, etc.).

This metadata is what we'll retrieve later to send to Gemini.

---

# therefore the updated Architecture will be like this

```text
PDF
 │
 ▼
Upload
 │
 ▼
Extract Text
 │
 ▼
Chunk Text
 │
 ▼
Embeddings
 │
 ▼
Qdrant Collection
```

---

#Why not store embeddings in SQLite? -SQLite can store vectors, but it doesn't support efficient nearest-neighbor search. Qdrant is built specifically for vector similarity search and scales much better.

#Why use cosine distance? -Cosine similarity compares the direction of vectors rather than their magnitude. It's commonly used with sentence embeddings because it captures semantic similarity effectively.

---

#Commit Once `test_qdrant.py` works: git is committed to every tests
After Qdrant is storing vectors, we'll build the most exciting feature:

```text
User Question
      │
      ▼
Generate Question Embedding
      │
      ▼
Search Qdrant
      │
      ▼
Top 5 Relevant Chunks
      │
      ▼
Send Context to Gemini
      │
      ▼
Grounded AI Answer
```

At this point, the project **Bilingual Tour Operations Agent** will become a true **RAG-powered enterprise AI assistant** that answers questions using uploaded travel documents instead of relying only on the LLM's general knowledge.


# Store Embeddings in Qdrant

```
nano app/services/vector_service.py
```

# Creating a new test:

```bash
nano test_store.py
```

TESTING THE ACTUAL RAG RETRIEVAL PIPELINE:
```bash
python test_store.py
```

but 'test_store.py' fails **not a bug in code**. It's a network request to Hugging Face timing out. also 'test_embedding.py' ran successfully already, the model is already downloaded and cached.During the next run, the `sentence-transformers` library is trying to check for additional files (such as adapter metadata), and Hugging Face is temporarily returning a 504 Gateway Timeout.

```
HTTP Error 504
https://huggingface.co/sentence-transformers/all-MiniLM-L6-v2/...
```
Many temporary 504 errors disappear on the second attempt.but it continues for this project..does the model is already cached?

```text
Exception ignored in:
QdrantClient.__del__()

ImportError: sys.meta_path is None,
Python is likely shutting down
```

This **isn't actually a failure**.

It happens because:

1. Your script finished successfully.
2. Python begins shutting down.
3. Qdrant tries to close itself.
4. Python has already unloaded some modules.
5. It prints this harmless warning.

This is a common destructor (`__del__`) warning.

---

verified with:
```bash
ls ~/.cache/huggingface/hub
```

for more details:

```bash
find ~/.cache -type d | grep all-MiniLM
```

This output confirms the model is **fully downloaded and cached**. FORCING IT TO WORK **OFFLINE** IS BETTER FOR DEVELOPMENT.

```
~/.cache/huggingface/hub/models--sentence-transformers--all-MiniLM-L6-v2
```

## UPDATING 'embedding_service.py' by replacing scripts in `app/services/embedding_service.py`
so modifying `embedding_service.py` to load the model **only from the local cache**, avoiding unnecessary online checks.

```python
from sentence_transformers import SentenceTransformer

# Load the model only from the local cache
model = SentenceTransformer(
    "all-MiniLM-L6-v2",
    local_files_only=True
)


def generate_embeddings(chunks: list[str]) -> list[list[float]]:
    """
    Generate vector embeddings for document chunks.
    """

    embeddings = model.encode(
        chunks,
        convert_to_numpy=True
    )

    return embeddings.tolist()
```


# testing again

```bash
python test_store.py
```

<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/5c1f11b0-54b3-4598-99b9-85a60a04ec9c" />


```bash
 Collection already exists.
 Stored 4 document chunks.
```
this proves that the **pipeline is completed**. if the caches agin appears or fails, verifying these scripts can help. 

```bash
cat app/services/embedding_service.py
```

```bash
cat app/services/vector_service.py
```

```bash
cat test_store.py
```





**current status** -- 
PDF Upload,PDF Parsing,Chunking,SentenceTransformer Embeddings,Local Qdrant Vector Database,Vector Storage was verified successfully.
**SentenceTransformer model loaded successfully.**
**Collection already exists.**
**Qdrant database is working. It found your existing collection instead of creating a new one.**
**Stored 4 document chunks**
**PDF was chunked and embedded successfully**


**CURRENT ARCHITECTURE**

```
PDF
   ↓
Chunking
   ↓
SentenceTransformer Embeddings
   ↓
Qdrant Vector DB
```



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
