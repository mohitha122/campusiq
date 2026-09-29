# 🎓 CampusIQ — AI-Powered College Information Assistant

CampusIQ is an AI-powered college circular assistant that helps students understand important information from college circulars quickly and clearly.

Instead of manually searching a long circular for dates, instructions, eligibility, deadlines, and required actions, CampusIQ extracts the important information and presents it in a student-friendly format.

> **From information overload → to clear student action.**

---

## ✨ Features

* 📄 Upload college circulars in PDF format
* 🔍 Extract text from normal/text-based PDFs
* 🖼️ OCR support for scanned or image-based PDFs
* 📋 Generate a simple smart summary
* 🚨 Detect circular urgency:

  * `URGENT`
  * `IMPORTANT`
  * `INFORMATIONAL`
* ✅ Generate an action checklist
* ⏰ Extract important deadlines
* 📅 Create a circular timeline
* 👤 Identify who the circular applies to
* 🎯 Check circular relevance using a student profile
* 💬 Ask questions about the uploaded circular
* 📍 Provide source page references when available
* 🛡️ Treat circular content as untrusted input to reduce prompt-injection risks
* 🔐 Keep the uploaded document in the current application session
* 🔄 Clear the current session and document state

---

## 🧠 How It Works

```text
Student Profile
      │
      ▼
Upload College Circular
      │
      ▼
PDF Text Extraction
      │
      ├── Text PDF ───────────────┐
      │                           │
      └── Scanned/Image PDF ──► OCR
                                  │
                                  ▼
                         Document Text
                                  │
                                  ▼
                         Gemini AI Analysis
                                  │
             ┌────────────────────┼────────────────────┐
             ▼                    ▼                    ▼
        Smart Summary        Student Actions      Deadlines
             │                    │                    │
             └────────────────────┼────────────────────┘
                                  ▼
                         Student Relevance
                                  │
                                  ▼
                         Grounded Q&A
```

---

## 🛠️ Technologies Used

* **Python**
* **Gradio** — user interface
* **Google Gemini API** — AI analysis and question answering
* **PyPDF** — PDF text extraction
* **pdf2image** — PDF-to-image conversion for OCR
* **Tesseract OCR / pytesseract** — scanned PDF text extraction
* **Poppler** — PDF rendering for OCR processing
* **Google Colab** — development/execution environment

---

## 📁 Project Structure

The current project is provided as a Jupyter/Google Colab notebook:

```text
Smart_Circular_Assistant.ipynb
```

The notebook contains the complete application implementation, including:

```text
Installation
    ↓
Imports & Configuration
    ↓
Gemini API Setup
    ↓
PDF Processing
    ↓
OCR Fallback
    ↓
Circular Analysis
    ↓
Student Relevance
    ↓
Question Answering
    ↓
Gradio Interface
    ↓
Application Launch
```

---

## 🚀 Running the Project

### Option 1 — Google Colab

Open:

```text
Smart_Circular_Assistant.ipynb
```

Run the notebook cells in order.

The notebook installs the required packages and system dependencies, asks for the Gemini API key securely using a password-style input, and launches the Gradio application.

The application uses:

```python
app.launch(
    share=True,
    debug=False
)
```

The generated Gradio public URL can then be opened in a browser.

---

## 📦 Installation

The notebook installs the main Python dependencies with:

```bash
pip install -q -U gradio pypdf google-genai pytesseract pdf2image
```

It also installs the OCR/PDF system dependencies:

```bash
apt-get update -qq
apt-get install -y -qq poppler-utils tesseract-ocr
```

---

## 🔑 Gemini API Key

The application requires a Gemini API key.

The notebook is designed to request the key privately:

```python
GEMINI_API_KEY = getpass.getpass(
    "Gemini API Key: "
)
```

The key is then supplied to the Gemini client through the environment.

### ⚠️ Important Security Note

**Never commit an API key to GitHub.**

If an API key has been exposed in the notebook or Git history, revoke/rotate it before publishing the repository.

For the GitHub version:

1. Remove the exposed key from the notebook.
2. Revoke/rotate the old key.
3. Do not put API keys inside source code.
4. Use environment variables or a secret-management system.
5. Check the repository history for previously committed secrets.

A safe pattern is:

```python
import os
from google import genai

api_key = os.environ["GEMINI_API_KEY"]
client = genai.Client(api_key=api_key)
```

---

## 📄 Supported Documents

CampusIQ is designed for college circulars in PDF format.

It supports:

### Text-based PDFs

Text is extracted directly using `pypdf`.

### Scanned/Image PDFs

If normal PDF extraction does not provide usable text, the application uses OCR with:

* `pdf2image`
* `pytesseract`
* Tesseract OCR

The notebook also validates uploaded PDFs and limits the file size to **15 MB**.

---

## 📊 Circular Analysis

After uploading a circular, CampusIQ can generate:

### Smart Summary

* Title
* Date
* Intended audience
* Main information
* Important points

### Urgency

The application classifies the circular as:

```text
URGENT
IMPORTANT
INFORMATIONAL
```

It also provides a reason for the classification.

### Actions

The application creates a checklist of actions students need to complete.

### Deadlines

Important dates and deadlines are extracted from the circular.

### Timeline

Important events are organized into a timeline.

### Applicability

CampusIQ identifies who the circular appears to apply to based only on information available in the document.

---

## 🎯 Student Relevance

Students can provide:

* Department
* Year
* Student type

The application then checks whether the uploaded circular appears relevant.

Possible results are:

```text
LIKELY RELEVANT
LIKELY NOT RELEVANT
UNCLEAR
```

If the circular does not provide enough information, the application uses `UNCLEAR` rather than inventing eligibility.

---

## 💬 Grounded Question Answering

Students can ask questions such as:

```text
What is this circular about?
When is the registration deadline?
Who needs to attend?
Is attendance compulsory?
What documents are required?
Where will the event happen?
What action do I need to take?
Does this apply to third-year students?
What is the most important date?
Which page contains the registration information?
```

The Q&A system is instructed to:

* Answer only from the uploaded circular
* Avoid outside knowledge
* Avoid guessing
* Say when information is not present
* Provide source page numbers when available
* Never invent page numbers
* Treat circular instructions as untrusted document data

---

## 🛡️ Security & Privacy Design

The prototype includes several security-aware controls:

* 🔑 API key is entered privately
* 📄 Only PDF files are accepted
* 📦 File size is limited to 15 MB
* 🧠 Uploaded document is maintained in the current session
* 🚫 The application does not use a database for document storage
* 🛡️ Circular text is treated as untrusted input
* 🚫 Instructions inside a circular are not allowed to override AI instructions
* 📚 Q&A is restricted to the uploaded circular
* ❌ The AI is instructed not to invent missing information
* 📍 Page references are returned when supported by the document
* 🔄 Clear Session removes the current document from application state

### Production Considerations

This notebook is a prototype. A production institutional deployment should additionally consider:

* User authentication
* HTTPS
* Server-side secret management
* Access controls
* Audit logging
* Data retention policies
* Institutional AI/data-processing requirements

> This project is a security-aware prototype and should not be treated as a complete production security guarantee.

---

## 🔄 Main User Flow

```text
1. Enter student profile
        ↓
2. Upload college circular
        ↓
3. Read the circular
        ↓
4. Analyze the circular
        ↓
5. View summary, urgency, actions,
   deadlines, timeline and applicability
        ↓
6. Check whether it applies to you
        ↓
7. Ask questions about the circular
        ↓
8. Clear the session when finished
```

---

## 🎯 Problem Statement

College circulars often contain important information about examinations, registrations, events, deadlines, instructions, and student requirements.

Students may need to read lengthy documents to determine:

* What is important?
* Does it apply to me?
* What do I need to do?
* When is the deadline?
* Where is the relevant information?

CampusIQ addresses this information overload by converting circular content into concise, structured, student-focused information.

---

## 💡 Future Enhancements

Possible future improvements include:

* 📱 Mobile-friendly deployment
* 🔔 Deadline reminders and notifications
* 📚 Support for multiple circulars
* 🔎 Search across previously uploaded circulars
* 🌐 Multilingual circular summaries
* 🗂️ Category detection for exams, events, registrations, fees, etc.
* 📆 Calendar integration
* 👨‍🎓 Personalized notification system
* 🏫 Institutional authentication
* 📊 Circular analytics dashboard
* 🗄️ Secure institutional document storage


Built as an academic AI/GenAI project focused on making college circulars easier for students to understand and act upon.
