# 📄 AI Resume Analyzer

An AI-powered resume analyzer that scores your resume for **ATS (Applicant Tracking System) compatibility** and gives actionable feedback — built and run entirely in Google Colab using the **Gemini API**.

Upload a PDF resume, and the notebook extracts the text, sends it to Gemini for analysis, and renders a clean visual dashboard with your ATS score, a summary, strengths, and weaknesses.

---

## 🚀 Features

- 📤 **PDF Upload** — Upload any resume in PDF format directly in Colab
- 🔍 **Text Extraction** — Parses resume content using `PyPDF2`
- 🤖 **AI-Powered Analysis** — Uses Google's Gemini API (`gemini-3.1-flash-lite`) to evaluate the resume like an ATS would
- 📊 **Visual Dashboard** — Renders results as a styled HTML dashboard (no external app needed) with:
  - A color-coded circular **ATS Score** (0–100)
  - A written **summary** of the resume's overall fit
  - A **Strengths** list
  - A **Weaknesses / areas to improve** list

---

## 🖼️ Example Output

The final cell renders a dashboard like this:

| ATS Score | Summary | Strengths & Weaknesses |
|-----------|---------|--------------------------|
| Circular score gauge (color-coded: red / yellow / green) | Short paragraph on overall resume fit | Side-by-side ✅ strengths and ⚠️ weaknesses cards |

---

## 🛠️ Tech Stack

| Component        | Tool/Library                          |
|-------------------|----------------------------------------|
| Environment       | Google Colab                          |
| PDF Parsing       | `PyPDF2`                              |
| AI Model          | Google Gemini API (`google-genai` SDK) — `gemini-3.1-flash-lite` |
| Dashboard Rendering | `IPython.display.HTML`              |
| Language          | Python 3                              |

---

## 📋 How It Works

1. **Install dependencies**
```python
   pip install openai PyPDF2
   pip install google-genai
```

2. **Upload your resume (PDF)**
```python
   from google.colab import files
   uploaded = files.upload()
   file_name = list(uploaded.keys())[0]
```

3. **Extract text from the PDF**
```python
   import PyPDF2

   def read_resume(file_path):
       text = ""
       with open(file_path, "rb") as file:
           reader = PyPDF2.PdfReader(file)
           for page in reader.pages:
               text += page.extract_text()
       return text
```

4. **Analyze the resume with Gemini**
```python
   from google import genai
   from google.colab import userdata

   client = genai.Client(api_key=userdata.get('GOOGLE_API_KEY'))

   def analyze_resume(text):
       response = client.models.generate_content(
           model="gemini-3.1-flash-lite",
           contents=f"""
           You are an ATS resume analyzer.
           Analyze the resume below and return ATS score and feedback.

           Resume:
           {text}

           Return in JSON format with:
           - ats_score (0-100)
           - summary
           - strengths
           - weaknesses

           Rules:
           - Output ONLY JSON
           - No extra text
           """
       )
       return response.text
```

5. **Render the results as a dashboard**
   The final cell parses the JSON response and displays a styled HTML dashboard with the score, summary, strengths, and weaknesses using `IPython.display.HTML`.

---

## ⚙️ Setup & Requirements

- A **Google Colab** account
- A **Gemini API key** ([Google AI Studio](https://aistudio.google.com/app/apikey))
- Store your key in Colab's **Secrets** manager as `GOOGLE_API_KEY` (🔑 icon in the left sidebar), so it can be accessed via:
```python
  from google.colab import userdata
  userdata.get('GOOGLE_API_KEY')
```

### Run it yourself
1. Open `AI_Resume_Analyzer.ipynb` in Google Colab
2. Add your `GOOGLE_API_KEY` to Colab Secrets
3. Run all cells in order
4. Upload your resume PDF when prompted
5. View your ATS dashboard at the bottom

---

## 📌 Notes & Limitations

- The `PyPDF2` text extraction works best on text-based PDFs (not scanned images) — if your resume is a scanned image, extraction quality will drop.
- Gemini's free-tier API can occasionally return `503 UNAVAILABLE` errors during high demand. If this happens, wait a few moments and re-run the analysis cell, or implement a retry loop with a fallback model.
- Results depend on the AI model's interpretation — scores are indicative, not a guarantee of real-world ATS performance.

---

## 🗺️ Roadmap / Ideas for Improvement

- [ ] Add retry logic with exponential backoff for API overload errors
- [ ] Expand the dashboard with sections like **Key Skills**, **Experience Summary**, and **Recommended Roles**
- [ ] Support multiple resume formats (DOCX, TXT)
- [ ] Turn this into a standalone web app (e.g. with Streamlit or Gradio) for use outside Colab
- [ ] Add job description matching to tailor ATS scoring to a specific role

---

## 📄 License

This project is open source and available for personal or educational use. Add a license of your choice (e.g. MIT) if publishing publicly.
