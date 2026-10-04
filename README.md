# AI-Assisted Exam Evaluator

An AI-assisted web application for evaluating **English subjective and creative examination answers** using OCR, Natural Language Processing (NLP), Machine Learning, and human-in-the-loop feedback.

The system is designed to reduce the time required for evaluating descriptive answers while keeping the grading process **transparent, reviewable, and adaptable to faculty corrections**.

---

## Overview

Evaluating subjective examination answers manually can be time-consuming, especially when a large number of answer sheets need to be assessed.

This project provides an automated first-pass evaluation system that:

- Extracts text from uploaded answer-sheet images.
- Preprocesses the extracted English text.
- Evaluates subjective answers using multiple similarity and coverage metrics.
- Evaluates creative answers using rubric-based LLM scoring.
- Generates marks, confidence scores, explanations, and feedback.
- Allows faculty members to manually review and correct AI-generated marks.
- Uses previous faculty corrections to calibrate future evaluations.
- Stores questions, evaluations, and feedback in MongoDB.

The system is intended to **assist faculty rather than completely replace human evaluation**.

---

## Key Features

### Answer Sheet Processing

- Upload answer-sheet images.
- Process multiple answer images for a question.
- Extract text from uploaded files through the browser-side OCR workflow.
- Combine extracted text before evaluation.

### Subjective Answer Evaluation

Subjective answers are evaluated using a hybrid scoring approach consisting of:

- TF-IDF cosine similarity
- Character n-gram cosine similarity
- Jaccard similarity
- Keyword coverage
- Sentence-level coverage
- Answer-length adequacy

The resulting metrics are combined into a weighted score to generate marks.

### Creative Answer Evaluation

Creative questions use a separate evaluation path based on:

- Question text
- Student answer
- Faculty-defined rubric
- LLM-based evaluation

The system produces:

- Marks
- Justification
- Strengths
- Areas for improvement

### Human-in-the-Loop Feedback

Faculty members can:

1. Review the generated marks.
2. Modify the marks when required.
3. Provide corrected evaluation data.
4. Store the correction for future calibration.

This creates a feedback loop between automated evaluation and human judgment.

### Feedback Calibration

Previous faculty-corrected answers can influence future evaluations.

The system compares a new answer with historically corrected answers and uses similar examples as weighted samples to adjust the initial score.

This helps the evaluator gradually align its output with previous faculty decisions while limiting the effect of individual feedback samples.

### Question Management

Faculty can create and manage:

- Question number
- Question text
- Question type
- Answer key
- Maximum marks
- Rubric criteria for creative questions

Questions and their latest evaluation results are stored in MongoDB.

---

# System Architecture

```text
                 Student Answer Sheet
                         │
                         ▼
                  Upload Image(s)
                         │
                         ▼
                    OCR Layer
              (Browser-side OCR flow)
                         │
                         ▼
                 Extracted Text
                         │
                         ▼
               Text Preprocessing
                         │
              ┌──────────┴──────────┐
              │                     │
              ▼                     ▼
      Subjective Answer       Creative Answer
          Evaluation              Evaluation
              │                     │
              ▼                     ▼
       Hybrid ML Scoring       LLM + Rubric
              │                     │
              └──────────┬──────────┘
                         ▼
                 Generated Results
                         │
                         ▼
              Faculty Review / Edit
                         │
                         ▼
                Feedback Storage
                         │
                         ▼
              Feedback Calibration
                         │
                         ▼
                    MongoDB
```

---

# Evaluation Pipeline

## Subjective Evaluation

```text
Student Answer
      │
      ▼
Text Preprocessing
      │
      ▼
TF-IDF Similarity
      │
      ├── Character Similarity
      │
      ├── Jaccard Similarity
      │
      ├── Keyword Coverage
      │
      ├── Sentence Coverage
      │
      └── Length Adequacy
      │
      ▼
Weighted Score
      │
      ▼
Feedback Calibration
      │
      ▼
Final Marks
      │
      ▼
Confidence + Explanation
```

The current subjective evaluator uses the following weights:

| Metric | Weight |
|---|---:|
| TF-IDF Cosine Similarity | 24% |
| Character Similarity | 22% |
| Jaccard Similarity | 14% |
| Keyword Coverage | 22% |
| Sentence Coverage | 12% |
| Length Adequacy | 6% |

The final percentage is multiplied by the question's maximum marks to obtain the generated score.

---

# Creative Evaluation Pipeline

```text
Question
   │
   ├── Rubric
   │
   └── Student Answer
          │
          ▼
       LLM Evaluation
          │
          ├── Marks
          ├── Justification
          ├── Strengths
          └── Improvements
          │
          ▼
   Feedback Calibration
          │
          ▼
      Final Result
```

Creative questions use the rubric defined during question creation and are evaluated through the frontend's LLM-based workflow.

---

# Technology Stack

| Layer | Technologies |
|---|---|
| Frontend | React 18, JavaScript, HTML, CSS |
| Backend | Python, FastAPI, Uvicorn |
| Database | MongoDB |
| NLP | NLTK |
| Machine Learning | Scikit-learn, TF-IDF, Cosine Similarity |
| Numerical Processing | NumPy |
| OCR / AI Processing | Browser-side Puter workflow |
| Version Control | Git, GitHub |

The current backend dependencies include FastAPI, Uvicorn, NLTK, Scikit-learn, NumPy, PyMongo, and multipart upload support.

---

# Language Support

> **Current implementation: English**

The current preprocessing pipeline is designed around English text. It:

- Converts text to lowercase.
- Removes characters outside `a-z`, `0-9`, and whitespace.
- Uses the NLTK English stop-word list.
- Uses English stop-word configuration in TF-IDF processing.

Therefore, **Tamil and other non-English languages are not supported by the current evaluation pipeline**.

Multilingual OCR and language-independent evaluation are considered future enhancements.

---

# Project Structure

```text
AI-assisted-exam-evaluator/
│
├── backend/
│   ├── main.py
│   ├── requirements.txt
│   └── uploads/
│
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── App.js
│   │   ├── QuestionContext.js
│   │   ├── QuestionDashboard.js
│   │   ├── QuestionUploader.js
│   │   ├── config.js
│   │   └── ...
│   └── package.json
│
├── README.md
└── .gitignore
```

---

# Database Design

The application uses MongoDB with three primary collections.

### `questions`

Stores:

- Question number
- Question type
- Question text
- Answer key
- Maximum marks
- Rubrics
- Uploaded answer metadata
- Extracted text

### `evaluations`

Stores:

- Question number
- Timestamp
- Generated marks
- Similarity breakdown
- Confidence score
- Evaluation details

### `feedback`

Stores:

- Question number
- Question type
- Previous marks
- Corrected marks
- Maximum marks
- Extracted answer text
- Faculty feedback
- Timestamp

The backend also creates indexes for question lookup, evaluations, and feedback history.

---

# API Endpoints

| Method | Endpoint | Purpose |
|---|---|---|
| `GET` | `/` | Check API status |
| `POST` | `/ocr` | Receive uploaded file and prepare it for OCR processing |
| `POST` | `/preprocess` | Preprocess extracted text |
| `POST` | `/evaluate-subjective` | Evaluate subjective answers |
| `POST` | `/feedback-calibration` | Apply historical feedback calibration |
| `GET` | `/questions` | Retrieve stored questions |
| `POST` | `/questions` | Create/update a question |
| `DELETE` | `/questions/{question_number}` | Delete a question |
| `POST` | `/evaluations` | Store an evaluation |
| `POST` | `/feedback` | Store faculty feedback |

The `/ocr` endpoint currently returns the uploaded file as a base64 data URL; the actual text extraction is performed through the frontend OCR workflow rather than by a dedicated server-side OCR engine.

---

# Installation

## 1. Clone the Repository

```bash
git clone https://github.com/aadhisesha/AI-assisted-exam-evaluator.git

cd AI-assisted-exam-evaluator
```

---

## 2. Backend Setup

Navigate to the backend directory:

```bash
cd backend
```

Install the Python dependencies:

```bash
pip install -r requirements.txt
```

Start the FastAPI server:

```bash
python main.py
```

The backend runs by default on:

```text
http://localhost:8000
```

FastAPI documentation will be available at:

```text
http://localhost:8000/docs
```

---

## 3. MongoDB Setup

Make sure MongoDB is running locally.

The application uses the following default configuration:

```text
MongoDB URI:
mongodb://localhost:27017

Database:
examcip
```

These values can be overridden using environment variables:

```text
MONGODB_URI
MONGODB_DB
```

For example:

```bash
set MONGODB_URI=mongodb://localhost:27017
set MONGODB_DB=examcip
```

On Linux/macOS:

```bash
export MONGODB_URI=mongodb://localhost:27017
export MONGODB_DB=examcip
```

---

## 4. Frontend Setup

Open another terminal and navigate to the frontend:

```bash
cd frontend
```

Install dependencies:

```bash
npm install
```

Start the React application:

```bash
npm start
```

The frontend will normally run at:

```text
http://localhost:3000
```

The project uses React 18 and Create React App's `react-scripts` development/build workflow.

---

# Usage

### Step 1 — Create a Question

Create a question from the Question Management section.

For a subjective question, provide:

- Question text
- Answer key
- Maximum marks

For a creative question, provide:

- Question text
- Rubric criteria
- Maximum marks

### Step 2 — Upload Student Answers

Select the appropriate question and upload the student's answer-sheet image(s).

### Step 3 — Extract Text

The uploaded answer is processed through the OCR workflow and converted into text.

### Step 4 — Evaluate

For subjective questions, the backend computes the hybrid ML score.

For creative questions, the answer is evaluated against the configured rubric using the LLM-based evaluation workflow.

### Step 5 — Review

The system displays:

- Generated marks
- Confidence score
- Similarity breakdown
- Matched keywords
- Missing keywords
- Evaluation explanation
- Strengths and improvements where applicable

### Step 6 — Faculty Correction

A faculty member can modify the generated score and submit the corrected evaluation.

### Step 7 — Calibration

The correction is stored and may be used as historical evidence when evaluating similar future answers.

---

# Why Hybrid Evaluation?

A single similarity metric is not sufficient for subjective answer evaluation.

For example:

- TF-IDF measures word and phrase overlap.
- Character n-grams provide some robustness to spelling and phrasing differences.
- Jaccard measures token-level overlap.
- Keyword coverage checks important concepts.
- Sentence coverage compares answer-key concepts at sentence level.
- Length adequacy helps identify extremely short answers.

Combining these signals produces a more transparent evaluation process than relying on a single score.

---

# Human-in-the-Loop Learning

The project follows a human-in-the-loop design:

```text
AI Evaluation
      │
      ▼
Faculty Review
      │
      ▼
Corrected Marks
      │
      ▼
Stored Feedback
      │
      ▼
Historical Similarity
      │
      ▼
Future Score Calibration
```

This approach allows faculty judgment to remain part of the grading process while enabling the system to use previous corrections to improve subsequent scoring.

---

# Current Limitations

The current version is a working academic prototype and has several limitations:

- English-language evaluation only.
- OCR extraction depends on the browser-side Puter workflow.
- Creative evaluation depends on LLM prompt/model behavior.
- No authentication or role-based access control.
- Development CORS configuration is currently permissive.
- The system should be treated as an **AI-assisted evaluator**, not an autonomous replacement for faculty grading.
- Production deployment would require stronger validation, security, monitoring, and access control.

---

# Future Enhancements

Planned improvements include:

- Multilingual OCR and evaluation
- Tamil and other Indian-language support
- Handwritten answer recognition improvements
- Transformer-based semantic similarity
- LLM-assisted rubric evaluation for more question types
- Bloom's Taxonomy-based answer analysis
- AI-assisted plagiarism and answer similarity detection
- Advanced analytics dashboard
- Faculty authentication and role-based access control
- Cloud deployment
- Batch evaluation of complete answer scripts
- Explainable AI-based grading reports
- Improved calibration using larger faculty-validated datasets

---

# Academic Context

This project was developed as an academic **Creative and Innovative Project** at the **College of Engineering Guindy, Anna University**.

The project explores the application of:

- Artificial Intelligence
- Machine Learning
- Natural Language Processing
- OCR
- Human-in-the-loop systems
- Automated assessment

to the problem of subjective examination evaluation.

---

# Project Team

**Aadhisesha D**  
**Roshan Kumar K**  
**Muhammed Sheik**  
**Karthik P**

**B.E. Computer Science and Engineering**  
**College of Engineering Guindy, Anna University**

---

# Disclaimer

This project is intended for **academic and research purposes**.

The generated score should be treated as an AI-assisted recommendation. Final academic grading should remain under the supervision and judgment of the authorized faculty member.
