## 🎯 Project Overview & Problem Statement

### The Challenge
Subjective examinations (essays, short answers, case studies) are vital for measuring high-level comprehension, critical thinking, and synthesis skills. However, manually grading subjective assessments introduces several friction points:
- **High Cognitive Load**: Instructors experience fatigue, leading to grading inconsistencies.
- **Handwriting Variability**: Poor legibility slows evaluation and risks misinterpretation.
- **Workflow Inefficiencies**: Physical papers must be collected, sorted, evaluated, and digitized.

### The Solution
The **AI-Assisted Subjective Exam Evaluator** bridges the gap between hand-written paper submissions and digital grading workflows. It provides:
*   **High-Fidelity Digitization**: Uses multi-modal Large Language Models optimized for document layouts.
*   **Structured Clean-up**: Automatically removes linguistic noise and standardizes formatting.
*   **Scoring-Ready Outputs**: Standardizes text to feed directly into semantic similarity engines.

---

## 🏗️ Architectural Blueprint

The application employs a **decoupled hybrid architecture** that optimizes API latency, compute costs, and client side processing power:

```
                  ┌──────────────────────────────────────────────┐
                  │              React Web Frontend              │
                  │   - Puter.js SDK (Client-side AI Orchestration)│
                  │   - File Upload Management & Visual Workspaces│
                  └──────┬──────────────────────────────▲────────┘
                         │                              │
        1. Upload File   │                              │ 7. Display Final Output
        (PDF/Image)      │                              │    (Raw & Preprocessed)
                         ▼                              │
             ┌───────────────────────┐                  │
             │    FastAPI Backend    │                  │
             │   - Base64 Encoding   │                  │
             │   - NLTK Tokenization │                  │
             └─────┬──────────▲──────┘                  │
                   │          │                         │
  2. Data URL      │          │ 5. Extracted            │
     Response      ▼          │    Raw Text             │
         ┌───────────────┐    │                         │
         │ Puter Cloud   ├────┼─────────────────────────┘
         │ (GPT-5 Nano)  │    │
         │ - Visual OCR  │    │ 6. POST /preprocess
         └───────────────┘    ▼ (For NLTK cleaning)
```

### Sequence Flow Detail
1. **Upload**: The user uploads a scanned JPEG, PNG, or PDF file of a handwritten answer sheet.
2. **Server Handshake (`/ocr`)**: The React frontend sends the binary payload to the FastAPI backend. FastAPI reads the raw bytes, converts them into a MIME-mapped Base64 Data URL, and immediately responds.
3. **Client-Side Vision OCR**: The React client uses the `puter.js` library to attach the Base64 Data URL directly to a prompt sent to `gpt-5-nano`. Because Puter's client-side library manages model communication directly from the user's browser, server-side payload limits and expensive OCR processing costs are avoided.
4. **NLP Processing (`/preprocess`)**: Once Puter returns the raw transcript, the frontend sends it back to FastAPI's `/preprocess` endpoint. Here, NLTK cleans the text on localized server memory.
5. **Render**: The React application renders the raw OCR output alongside the preprocessed token output in a dual-workspace UI.

---

##  Deep-Dive Tech Stack

### Frontend Core
*   **Library**: React 18 (Hooks, functional updates, async state handling).
*   **OCR Integration**: Puter.js SDK (CDN-loaded client library accessing distributed serverless LLM endpoints).
*   **Design Framework**: Custom vanilla CSS employing CSS variables, responsive flex/grid wrappers, custom-designed dark elements, and CSS animations.

### Backend Core
*   **Framework**: FastAPI 0.104 (ASGI Python Framework utilizing Starlette for web routes and Pydantic for data type enforcement).
*   **ASGI Server**: Uvicorn 0.24.
*   **NLP Tooling**: NLTK (Natural Language Toolkit) for English-language corpus tokenization and stopword removal lists.

---

##  Functional Modules

The project is structured logically to separate backend services from frontend UI assets:

```
exam_eval/
├── backend/
│   ├── main.py              # FastAPI Application & Preprocessing logic
│   ├── requirements.txt      # Python package versions
│   └── uploads/             # Server-side transient storage (created automatically)
├── frontend/
│   ├── public/
│   │   └── index.html       # Entrypoint page mounting Puter CDN
│   ├── src/
│   │   ├── App.js           # Core state coordinator & API requester
│   │   ├── App.css          # Styling stylesheet & design tokens
│   │   └── index.js         # React DOM render script
│   └── package.json         # Node metadata and dependencies
└── README.md                # Project documentation
```

### Code Roles
*   [`backend/main.py`](file:///c:/Users/ARAVINDH%20S/Desktop/dontKnowWhatThisIs/exam_eval/backend/main.py): Contains endpoints `/ocr` and `/preprocess`, CORS configuration, and text parsing algorithms.
*   [`frontend/src/App.js`](file:///c:/Users/ARAVINDH%20S/Desktop/dontKnowWhatThisIs/exam_eval/frontend/src/App.js): Handles state transitions (`loading`, `error`, `extractedText`, `processedText`), triggers scripts injection for Puter SDK, and manages async network streams.
*   [`frontend/src/App.css`](file:///c:/Users/ARAVINDH%20S/Desktop/dontKnowWhatThisIs/exam_eval/frontend/src/App.css): Layout grid templates, responsive break-points, design tokens (borders, margins, font pairings).

---

##  Preprocessing Pipeline Internals

Before subjective answers can be analyzed for semantic grading, text must be standardized to eliminate syntax variations. The preprocessing pipeline runs the following sequential operations:

1.  **Lowercasing**: Normalizes the entire corpus to lower-case.
    ```python
    text = text.lower()
    ```
2.  **Punctuation and Special Character Stripping**: Removes syntactic characters while preserving alphanumeric strings and single whitespace.
    ```python
    text = re.sub(f"[^a-z0-9\s]", "", text)
    ```
3.  **Whitespace Normalization**: Collapses sequential tabs, newlines, and double spaces into a single space.
    ```python
    text = re.sub(r"\s+", " ", text).strip()
    ```
4.  **Stopwords Elimination**: Uses NLTK's English stopword corpus to filter out terms with low informational value (e.g., `the`, `a`, `about`, `above`, `after`).
    ```python
    stop_words = set(stopwords.words('english'))
    filtered_words = [w for w in text.split() if w not in stop_words]
    processed_text = " ".join(filtered_words)
    ```

### Example Transformation:
*   **Raw OCR Input**: *"The quick, brown Fox jumps over the lazy Dog! It was a very close jump."*
*   **Normalized (Step 1-3)**: *"the quick brown fox jumps over the lazy dog it was a very close jump"*
*   **Final Preprocessed (Step 4)**: *"quick brown fox jumps lazy dog close jump"*

---

##  Local Setup & Environment Configuration

### System Requirements
*   **OS**: Windows 10/11, macOS Catalina or higher, Ubuntu 20.04+.
*   **Runtimes**: Python 3.8+ and Node.js v16+ with npm.

### Backend Installation
1. Navigate to the backend folder:
   ```bash
   cd backend
   ```
2. Set up a Python Virtual Environment:
   ```bash
   # Windows PowerShell
   python -m venv venv
   .\venv\Scripts\Activate.ps1

   # Windows Command Prompt
   python -m venv venv
   .\venv\Scripts\activate.bat

   # macOS/Linux Terminal
   python3 -m venv venv
   source venv/bin/activate
   ```
3. Install the required Python packages:
   ```bash
   pip install -r requirements.txt
   ```
4. Download NLTK Corpora (FastAPI handles this automatically on startup, but you can preload it):
   ```bash
   python -c "import nltk; nltk.download('stopwords')"
   ```
5. Run the development server:
   ```bash
   python main.py
   ```
   Confirm the backend is running by loading `http://localhost:8000/` in your browser. You should receive `{"message": "Exam Evaluator API - OCR Service Ready"}`.

### Frontend Installation
1. Open a new terminal instance and navigate to the frontend directory:
   ```bash
   cd frontend
   ```
2. Install npm dependencies:
   ```bash
   npm install
   ```
3. Start the React live-reload server:
   ```bash
   npm start
   ```
   This will spin up a development web server on `http://localhost:3000`.

---

##  API Specification

The API is fully documented out-of-the-box using FastAPI's automatic Swagger implementation, accessible at `http://localhost:8000/docs`.

### 1. Document Extraction Endpoint (`POST /ocr`)
Takes binary image/PDF files and serializes them to base64 schema format.
*   **URL**: `/ocr`
*   **Method**: `POST`
*   **Payload Format**: `multipart/form-data`
*   **Parameters**:
    *   `file`: Binary file (mime types: `image/png`, `image/jpeg`, `image/jpg`, `application/pdf`)
*   **Success Response (Status: 200 OK)**:
    ```json
    {
      "data_url": "data:image/png;base64,iVBORw0KGgoAAAANS...",
      "file_name": "exam_sheet_student1.png"
    }
    ```
*   **Error Response (Status: 200 OK with internal message details)**:
    ```json
    {
      "error": "Detailed error string",
      "data_url": "",
      "file_name": ""
    }
    ```

### 2. NLP Pipeline Preprocessing Endpoint (`POST /preprocess`)
Receives plain text data, sanitizes formatting, and returns tokenized output.
*   **URL**: `/preprocess`
*   **Method**: `POST`
*   **Payload Format**: `application/json`
*   **Schema**:
    ```json
    {
      "text": "String text content extracted from LLM"
    }
    ```
*   **Success Response (Status: 200 OK)**:
    ```json
    {
      "extracted_text": "String text content extracted from LLM",
      "processed_text": "sanitized tokenized text content"
    }
    ```

---

##  Troubleshooting & FAQ

### Issue: "Puter library did not load within 10 seconds" or script errors in browser console
*   **Reason**: The Puter AI SDK loads asynchronously via CDN (`https://js.puter.com/v2/`). If your network blocks this host or if you are running offline, the script will fail to mount.
*   **Remedy**: Ensure you have an active internet connection. If behind a corporate proxy, whitelist `js.puter.com` and `puter.com`.

### Issue: NLTK lookup error `LookupError: No resource 'corpora/stopwords'`
*   **Reason**: Python cannot download or write the NLTK stopword corpus to your user directory due to write permission constraints or proxy limits.
*   **Remedy**: Manually download the stopwords zip from NLTK's website, unzip it, and place the folders inside your system's global search path (e.g., `C:\Users\<user>\AppData\Roaming\nltk_data\corpora\stopwords`).

### Issue: CORS blocks React frontend from posting to FastAPI backend
*   **Reason**: FastAPI's CORS configuration might not match the client port.
*   **Remedy**: Inspect `backend/main.py`. The current configuration has `allow_origins=["*"]` configured. If issues persist, replace `"*"`, with your explicit origin: `["http://localhost:3000"]`.

---

##  Mathematical Concepts & Phase 2 Roadmap

Phase 2 will introduce automated grading using mathematical NLP evaluation models to compare student answers to a master answer key.

```
                   ┌──────────────────────────────────────────────┐
                   │           Preprocessed Clean Text            │
                   └──────────────────────┬───────────────────────┘
                                          ▼
                   ┌──────────────────────────────────────────────┐
                   │             TF-IDF Vectorization             │
                   │   Transforms text tokens into numerical      │
                   │   frequency vectors in multidimensional space.│
                   └──────────────────────┬───────────────────────┘
                                          ▼
                   ┌──────────────────────────────────────────────┐
                   │              Cosine Similarity               │
                   │   Measures angular deviation between student │
                   │   and model vectors to calculate score.      │
                   └──────────────────────────────────────────────┘
```

### 1. TF-IDF (Term Frequency-Inverse Document Frequency)
TF-IDF calculates how important a word is to a document relative to a corpus of documents:

$$\text{TF}(t, d) = \frac{\text{Count of } t \text{ in } d}{\text{Total words in } d}$$

$$\text{IDF}(t, D) = \log \left( \frac{\text{Total number of documents } |D|}{\text{Number of documents containing } t} \right)$$

$$\text{TF-IDF}(t, d, D) = \text{TF}(t, d) \times \text{IDF}(t, D)$$

### 2. Cosine Similarity Formula
Cosine similarity evaluates the conceptual similarity between the reference answer vector ($\mathbf{A}$) and the student response vector ($\mathbf{B}$):

$$\text{Similarity}(\mathbf{A}, \mathbf{B}) = \cos(\theta) = \frac{\mathbf{A} \cdot \mathbf{B}}{\|\mathbf{A}\| \|\mathbf{B}\|} = \frac{\sum_{i=1}^{n} A_i B_i}{\sqrt{\sum_{i=1}^{n} A_i^2} \sqrt{\sum_{i=1}^{n} B_i^2}}$$

*   A output score near **1.0** indicates identical semantic term distributions.
*   A score near **0.0** indicates no common semantic ground.

---

##  Dockerization & Deployment Guide

For sandbox development or production hosting, you can package both components using Docker containers:

### Dockerfile for Backend (`backend/Dockerfile`)
```dockerfile
FROM python:3.10-slim

WORKDIR /app

# Install system dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Download NLTK data
RUN python -c "import nltk; nltk.download('stopwords')"

COPY . .

EXPOSE 8000

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### Dockerfile for Frontend (`frontend/Dockerfile`)
```dockerfile
FROM node:18-alpine as build

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

# Nginx stage to serve static web content
FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### Orchestration with Docker Compose (`docker-compose.yml`)
```yaml
version: '3.8'

services:
  backend:
    build: ./backend
    ports:
      - "8000:8000"
    volumes:
      - ./backend/uploads:/app/uploads

  frontend:
    build: ./frontend
    ports:
      - "3000:80"
    depends_on:
      - backend
```

To boot the entire cluster, execute:
```bash
docker-compose up --build
```

---

##  Contribution & Coding Standards

### Git Branching Strategy
*   **Main Branch**: Stable production releases.
*   **Develop Branch**: Staging area for features ready for verification.
*   **Feature Branches (`feature/name-here`)**: Active sandbox for new enhancements.

### Linting & Formatting Rules
*   **Python**: Follow PEP 8 guidelines. Format code using `black` or `autopep8`.
*   **JavaScript**: Write ES6+ clean syntax. Use standard functional component hooks in React.
