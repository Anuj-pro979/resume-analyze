# AI-Powered Resume Relevance Check System

> **Project**: Resume screening Streamlit app (uploaded script: `resume_screening_fixed.py`)

---

## Overview

This repository contains a Streamlit application that automates resume screening by:

* Parsing Job Descriptions (JD) and candidate resumes (PDF/DOCX) using multiple extraction libraries.
* Extracting structured data (skills, contact info, experiences) via an optional Gemini AI parser (with robust fallbacks).
* Performing **hard** (keyword/skill) and **semantic** matching to compute scores and generate feedback.
* Displaying results with dashboards, candidate cards, shortlists and downloadable reports.

This README provides a step-by-step code analysis, quickstart/run instructions, a mermaid architecture flowchart, and a placeholder area to include a demo video for the project.

---

## Quick Links

* **Main script**: `resume_screening_fixed.py`
* **Demo upload**: instructions & example Streamlit snippet included below
* **Architecture**: Mermaid flowchart (in the Architecture section)

---

## Minimum Requirements

* Python 3.8+
* Recommended libraries (install via `pip`):

```bash
pip install streamlit pandas plotly plotly-express google-generativeai PyPDF2 python-docx numpy
# Optional (for better PDF/DOCX handling):
pip install pdfplumber PyMuPDF docx2txt textract
```

> **Note**: Gemini API features require `GEMINI_API_KEY` saved in `.streamlit/secrets.toml` as `GEMINI_API_KEY = "<your_key>"`. Without it the app uses fallback text-matching logic.

---

## Quick Start (Run locally)

1. Clone the repo or place `resume_screening_fixed.py` in a folder.
2. (Optional) Create `.streamlit/secrets.toml` and add your Gemini API key.

```toml
# .streamlit/secrets.toml
GEMINI_API_KEY = "your_api_key_here"
```

3. Install dependencies (see above).
4. Run the app:

```bash
streamlit run resume_screening_fixed.py
```

Open the Streamlit UI in your browser (usually `http://localhost:8501`).

---

## Where to Upload Demo Video (placeholder)

This README includes a dedicated **Demo Video** section and a Streamlit snippet so you can upload and play a demo video inside the app.

**Suggested repo structure for demo assets**:

```
project_root/
├── assets/
│   └── demo.mp4     # put demo video here (optional)
├── resume_screening_fixed.py
└── .streamlit/
    └── secrets.toml
```

**Streamlit snippet (to add a demo page or widget)**

```python
# Add to the Streamlit app (e.g. new page or within `main()` routing)
import streamlit as st

def demo_page():
    st.header("Demo Video")
    st.write("Upload or play the demo explaining the app flow")
    demo_file = st.file_uploader("Upload demo video (mp4/mov/avi)", type=["mp4","mov","avi"])
    if demo_file:
        st.video(demo_file)
    else:
        # If you have an assets/demo.mp4 in repo
        try:
            with open("assets/demo.mp4","rb") as f:
                st.video(f.read())
        except FileNotFoundError:
            st.info("No demo video found. Upload one above or place `assets/demo.mp4` in the repo.")

# Then register the page in your navigation routing
# e.g. add "Demo" to the `page` selectbox in `main()` and call demo_page() when selected
```

Place your demo video at `assets/demo.mp4` or upload directly in the app using the uploader. The README includes a visible placeholder header so a demo link or embedded video can be added on the repository's front page or the app's help page.

---

## Architecture (Mermaid flowchart)

```mermaid
flowchart LR
    User[User / Recruiter] --> UI[Streamlit UI]
    UI -->|Upload JD & Resumes| UPLOAD[File Upload]
    UPLOAD --> DocParser[DocumentParser]
    DocParser --> Parser[GeminiParser (AI)\nor Fallback Parsers]
    Parser -->|Structured Data| JD[JobDescription]
    Parser -->|Structured Data| RES[ResumeData]
    JD & RES --> Analyzer[RelevanceAnalyzer (hard + semantic)]
    Analyzer --> Feedback[FeedbackGenerator]
    Feedback --> DB[DatabaseManager / session_state]
    DB --> UIResults[Results / Shortlist / Analytics]

    Parser -->|optional| ExternalLibs[PDF/DOCX libs (PyPDF2, pdfplumber, PyMuPDF, docx2txt, textract)]
    Parser -->|calls| Gemini[Gemini API]
    UIResults -->|download| Export[CSV / Reports]
    UI --> Demo[Demo Video Uploader / Player]

```

> This diagram shows the core processing pipeline: upload → parse → analyze → feedback → store → display.

---

## Step-by-step Code Walkthrough

Below is a concise explanation of the main modules and how data flows through them. (If you want, I can also produce a more granular line-by-line annotated version.)

### 1. `DocumentParser`

* Purpose: Robustly extract text from uploaded PDF/DOCX using multiple libraries in fallback order.
* Methods:

  * `extract_text_from_pdf_multiple_methods(file_bytes)` — tries `PyPDF2`, `pdfplumber`, `PyMuPDF`, then `textract`.
  * `extract_text_from_docx_multiple_methods(file_bytes)` — tries `python-docx`, `docx2txt`, then `textract`.
  * `extract_text_from_uploaded_file(uploaded_file)` — dispatcher by file type.
* Notes: Good fallback strategy. Consider adding OCR (Tesseract) for scanned PDFs.

### 2. `GeminiParser`

* Purpose: Use Google `genai` to extract structured JSON (job/ resume) when API key is available. If not, uses fallback regex-based parsing.
* Key methods:

  * `parse_job_description(jd_text)` — prompts Gemini to return a strict JSON schema for JDs.
  * `parse_resume(resume_text, filename)` — prompts Gemini to return a strict JSON schema for resumes.
  * `_fallback_jd_parsing()` & `_fallback_resume_parsing()` — regex and keyword heuristics.
* Notes: The fallback parsing contains lists of common skills and patterns to extract name, email, phone, etc. Good defensive approach when AI is unavailable.

### 3. `RelevanceAnalyzer`

* Purpose: Compute two complementary scores:

  1. **Hard match** — direct skill/keyword/requirement matches with weighted scoring.
  2. **Semantic match** — a simple word overlap similarity (set-based) to capture contextual alignment.
* Output: A numeric score and a detailed breakdown dictionary for explainability.

### 4. `FeedbackGenerator`

* Purpose: Produce human-readable feedback; if Gemini is available it will generate polished feedback via LLM prompts; otherwise it composes analytic feedback using the scores and match data.
* Notes: The fallback feedback is multi-part and constructive.

### 5. `DatabaseManager`

* Purpose: Store screening results in `st.session_state.screening_results` (in-memory). Also exposes read/clear operations.
* Notes: For production use migrate to persistent DB (SQLite/Postgres) with proper indices and retention policies.

### 6. `ResumeScreeningSystem`

* Purpose: Orchestrator that ties together: parser, analyzer, db, feedback generator, and document parser.
* Key flows:

  * `process_job_description()` — returns `JobDescription` object.
  * `process_resume()` — returns `ResumeData` object.
  * `screen_candidate(jd, resume)` — does analysis, generates `ScreeningResult`, feedback and stores it.
  * `batch_process_resumes()` — loops over multiple resume files and displays progress.

### 7. Streamlit UI Functions

* `main()` — top-level routing: pages: Home, Analytics, Results History, Shortlisted, Help.
* `home_page()` — JD + resumes upload, configure batch options and start screening.
* Several helper renderers: `display_candidate_card()`, `show_detailed_candidate_report()`, `generate_shortlist_report()`, `export_email_list()`, `download_full_report()`, `analytics_page()`, `results_history_page()`, `help_page()`.

---

## Where data flows (simplified)

1. User uploads JD (PDF/DOCX) and multiple resumes via Streamlit UI.
2. `DocumentParser` extracts text; if successful, `GeminiParser.parse_*` builds structured objects.
3. `ResumeScreeningSystem.screen_candidate()` calls `RelevanceAnalyzer` and `FeedbackGenerator` to compute scores and feedback.
4. `DatabaseManager` stores results in session state.
5. UI renders dashboards, candidate cards and exportable reports.

---

## Production Readiness & Improvements (suggested)

**Short-term (low-effort)**

* Add logging (Python `logging`) instead of silent except/pass.
* Improve error handling and user-visible error messages for file parsing failures.
* Add unit tests for parsing & scoring logic.
* Add caching for parsed JDs / resumes to avoid double parsing.

**Medium-term**

* Replace `st.session_state` with a persistent store (Postgres, SQLite with WAL, or a cloud DB).
* Use background workers (Celery / RQ) for heavy processing and long-running AI calls.
* Use embeddings + vector DB (e.g., Faiss, Milvus) for better semantic matching.
* Add OCR (Tesseract) for scanned PDFs and images inside PDFs.
* Use a robust NER (spaCy) to extract entities (names, companies, roles).

**Security & Privacy**

* Mask or pseudonymize PII before sending to external APIs when possible.
* Obtain candidate consent if you send resumes to third-party AI services.
* Secure secrets (use environment variables / cloud secret managers).

---

## Troubleshooting & Common Issues

* If AI features fail: ensure `.streamlit/secrets.toml` is present and valid and restart the app.
* If PDF text extraction fails: try installing `pdfplumber` and `PyMuPDF`.
* If resumes are scanned images: add OCR preprocessing.

---

## Example: Add a "Demo" page to the app (summary)

The demo page snippet earlier in this README shows how to allow uploading or serving a local demo video. Add the snippet and register the page in the `main()` navigation selectbox.

---

## License

MIT License — adapt and reuse freely.

---

## Contact / Next Steps

If you want:

* A line-by-line annotated version of the script.
* I can automatically patch your `resume_screening_fixed.py` to add the demo page and register it in the navigation.
* A Dockerfile, `requirements.txt`, or CI workflow for testing and deployment.

<!-- End of README -->
