# 🚀 AI-Powered Resume Screening App

VISIT MY WEBSITE AT https://resumeo3.streamlit.app/

Streamline hiring with an AI-assisted tool that quickly screens resumes against job descriptions. Built with **Streamlit** and optional **Gemini AI** for smarter parsing.

---

## ✨ Features

* 📂 Upload Job Description (PDF/DOCX)
* 📑 Upload multiple resumes (PDF/DOCX)
* ⚡ Extract text using robust fallback parsers
* 🤖 AI-powered parsing (Gemini API) or fallback regex
* 🎯 Hard skill & semantic matching for fair scoring
* 📊 Dashboard with results, analytics & shortlists
* 📝 Downloadable reports and email list
* 🎥 Demo video support (upload/play inside app)

---

## 🛠️ Setup

### Requirements

* Python 3.8+
* Install dependencies:

```bash
pip install streamlit pandas plotly google-generativeai PyPDF2 python-docx numpy
pip install pdfplumber PyMuPDF docx2txt textract   # optional, for better parsing
```

### (Optional) Gemini API

Create `.streamlit/secrets.toml`:

```toml
GEMINI_API_KEY = "your_api_key_here"
```

### Run

```bash
streamlit run resume_screening_fixed.py
```

👉 App opens at `http://localhost:8501`

---

## 🎥 Demo Video

Add your demo at `assets/demo.mp4` or upload via app.

```python
st.video("assets/demo.mp4")
```

---

## 🔎 Architecture

```mermaid
flowchart LR
    User[Recruiter] --> UI[Streamlit UI]
    UI --> Upload[Upload JD & Resumes]
    Upload --> Parse[Document + AI Parser]
    Parse --> JD[Job Description]
    Parse --> Resume[Resume Data]
    JD & Resume --> Analyze[Relevance Analyzer]
    Analyze --> Feedback[Feedback Generator]
    Feedback --> Results[Dashboard & Reports]
```

---

## ✅ Perfect for Hackathons

* Clear **workflow**
* Clean **UI with Streamlit**
* Scalable idea → add OCR, embeddings, or DB later

---

## 📜 License

MIT License — use freely.

---

💡 *Tip: Keep demo simple, show upload → scoring → results in action!*




[![Your Video Title](https://img.youtube.com/vi/1M9ClAnGqJY/0.jpg)](https://www.youtube.com/watch?v=1M9ClAnGqJY)



<img width="1920" height="1020" alt="Screenshot 2025-09-21 041653" src="https://github.com/user-attachments/assets/536212e3-de6c-4e97-a378-93d5388314c3" />
                                    |
                              SELECT  JD                   
<img width="1902" height="1002" alt="Screenshot 2025-09-21 041722" src="https://github.com/user-attachments/assets/79cff9a9-4e45-46e9-bfbc-bdeb062cae91" />
                                     |
                       SELECT MULTIPLE RESUMES 
<img width="1902" height="1002" alt="Screenshot 2025-09-21 041757" src="https://github.com/user-attachments/assets/1c6233c1-3f4c-437d-8c96-e9e879794857" />
                                     |
                        CLICK ON BUTOON TO PROCESS 
<img width="1920" height="1020" alt="Screenshot 2025-09-21 041818" src="https://github.com/user-attachments/assets/8884535f-8ab5-45f6-8459-7a5f5adde3f3" />
                                     |
<img width="1920" height="1020" alt="Screenshot 2025-09-21 043045" src="https://github.com/user-attachments/assets/ee3776a2-4ece-465a-9208-9f5b1a626be6" />
                                      |
                           Analytics & Insights ON LEFT     
<img width="1920" height="1020" alt="Screenshot 2025-09-21 043452" src="https://github.com/user-attachments/assets/4be24b8b-0f31-495f-8795-bfc5a57232cc" />
                                      |
                             Screening History
<img width="1920" height="1020" alt="Screenshot 2025-09-21 043429" src="https://github.com/user-attachments/assets/726da454-a2e9-4394-9ac9-15cadc736468" />
