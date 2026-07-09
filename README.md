#  AI-Assisted Exam Evaluator

> An AI-powered web application that automates subjective answer evaluation using OCR, Machine Learning, and NLP, while continuously improving grading accuracy through faculty feedback.

---

#  Overview

Manual evaluation of descriptive answer sheets is time-consuming, inconsistent, and prone to human error. This project automates the evaluation process by combining Optical Character Recognition (OCR), Natural Language Processing (NLP), and Machine Learning techniques to generate marks and feedback for subjective answers.

The system supports both **English and Tamil** answer sheets, allows faculty to review AI-generated marks, and continuously improves its scoring using an **Auto-Tuning Correction Mechanism** based on faculty feedback.

---

##  Academic Project

This project was developed as part of the **Creative and Innovative Project** course at the **College of Engineering Guindy (Anna University)**. It aims to demonstrate the application of Artificial Intelligence, Machine Learning, Optical Character Recognition (OCR), and Natural Language Processing (NLP) to automate the evaluation of subjective answer sheets.

---

#  Features

- 📄 Upload answer sheets (PDF/Image)
- 🔍 OCR extraction for English and Tamil answers
- 🧹 Automatic text preprocessing
- 🤖 AI-assisted subjective answer evaluation
- 📊 Machine Learning-based semantic similarity scoring
- 📝 Keyword and sentence similarity analysis
- 💯 Automatic mark generation
- 💬 AI-generated feedback
- 👨‍🏫 Faculty review and manual mark editing
- 🔄 Auto-Tuning Correction mechanism
- 📚 Question management system
- 📈 Evaluation history and reports
- 💾 MongoDB database integration
- 🌐 Responsive React frontend

---

#  System Architecture

```text
                Student Answer Sheet
                        │
                        ▼
                 Upload PDF/Image
                        │
                        ▼
                  OCR Extraction
                        │
                        ▼
                 Text Preprocessing
                        │
                        ▼
               Feature Extraction
                        │
                        ▼
           Machine Learning Evaluation
                        │
                        ▼
             Marks & Feedback Generation
                        │
                        ▼
               Faculty Review/Edit Marks
                        │
                        ▼
             Auto-Tuning Learning Module
                        │
                        ▼
                    MongoDB Database
```

---

#  Machine Learning Pipeline

```
Input Answer
      │
      ▼
OCR
      │
      ▼
Preprocessing
      │
      ▼
TF-IDF Vectorization
      │
      ▼
Cosine Similarity
      │
      ▼
Keyword Coverage
      │
      ▼
Sentence Similarity
      │
      ▼
Weighted Score
      │
      ▼
Feedback Generation
      │
      ▼
Faculty Review
      │
      ▼
Auto-Tuning Model
```

---

#  Auto-Tuning Correction Mechanism

One of the key features of this project is its adaptive correction system.

Whenever a faculty member edits the AI-generated marks:

- The corrected marks are stored in the database.
- The system learns from these corrections.
- Machine Learning techniques adjust future evaluations.
- Similar answers receive more accurate marks over time.

This enables the evaluator to continuously improve its scoring accuracy based on real faculty feedback.

---

#  Tech Stack

| Category | Technologies |
|----------|--------------|
| Frontend | React, HTML, CSS, JavaScript |
| Backend | FastAPI, Python |
| Database | MongoDB |
| OCR | EasyOCR (Tamil), Puter AI OCR (English) |
| Machine Learning | Scikit-learn, TF-IDF, Cosine Similarity |
| Libraries | NumPy, Pandas, OpenCV |
| Version Control | Git, GitHub |

---

#  Project Structure

```
AI-Assisted-Exam-Evaluator/

├── frontend/
│   ├── src/
│   ├── public/
│   └── package.json
│
├── backend/
│   ├── routes/
│   ├── models/
│   ├── preprocessing/
│   ├── ml/
│   ├── ocr/
│   └── main.py
│
├── database/
│
├── assets/
│
└── README.md
```

---

#  Installation

### Clone the Repository

```bash
git clone https://github.com/yourusername/AI-Assisted-Exam-Evaluator.git

cd AI-Assisted-Exam-Evaluator
```

### Backend

```bash
cd backend

pip install -r requirements.txt

python main.py
```

### Frontend

```bash
cd frontend

npm install

npm start
```

---

#  API Endpoints

| Method | Endpoint | Description |
|---------|----------|-------------|
| POST | `/ocr` | Extract text from uploaded answer sheet |
| POST | `/preprocess` | Clean and preprocess extracted text |
| POST | `/evaluate` | Evaluate subjective answers |
| POST | `/feedback` | Store faculty corrections |
| GET | `/questions` | Retrieve question bank |
| POST | `/questions` | Add new questions |

---

#  Evaluation Methodology

The evaluation score is generated using multiple Machine Learning techniques:

- TF-IDF Vectorization
- Cosine Similarity
- Keyword Matching
- Sentence Similarity
- Coverage Analysis
- Length Adequacy
- Weighted Scoring

These metrics are combined to generate accurate marks and meaningful feedback.

---


#  Future Enhancements

- Large Language Model (LLM) based evaluation
- Handwriting recognition
- Multi-language support
- AI plagiarism detection
- Bloom's Taxonomy evaluation
- Advanced analytics dashboard
- Cloud deployment
- Voice answer evaluation

---


# 👨‍💻 Project Team

This project was developed as part of the **Creative and Innovative Project** course by:

- **Aadhisesha D**
- **Roshan Kumar K**
- **Muhammed Sheik**
- **Karthik P**

**B.E. Computer Science Engineering**  
**College of Engineering Guindy (Anna University)**
