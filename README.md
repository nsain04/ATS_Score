# 🎯 AI-Powered ATS Resume Analyzer & Scorer

An intelligent, full-stack Resume & ATS (Applicant Tracking System) Analyzer built with **FastAPI**, **Streamlit**, **spaCy**, **Sentence Transformers**, and **Groq LLM**. It evaluates resumes against job descriptions, validates technical and soft skills, provides actionable feedback, and exports comprehensive PDF reports.

---

## 🏗️ Architecture Overview

The project is structured into two decoupled, scalable tiers:

```
ai-resume-ats/
├── backend/                  # FastAPI REST API (NLP, ML Embeddings, LLM Feedback)
│   ├── api/                  # API Endpoints (/analyze-resume, /history, /health, /generate-pdf)
│   ├── core/                 # App configuration & CORS settings
│   ├── database/             # Supabase async client for storing analysis history
│   ├── models/               # Pydantic schemas and validation
│   ├── services/             # Core engines: ATS scoring, Groq parser, JD matcher, PDF export
│   ├── templates/            # Jinja2 HTML templates for PDF generation
│   └── requirements.txt      # Backend-specific dependencies
├── frontend/                 # Streamlit Web Application (Interactive UI & Auth)
│   ├── .streamlit/           # Theme config and secrets templates
│   ├── assets/               # Custom CSS styles
│   ├── components/           # Reusable UI components (header, charts, cards)
│   ├── services/             # API client & Supabase OAuth/Auth client
│   ├── views/                # Views: Landing, Scorer, History, Resources
│   └── requirements.txt      # Lightweight frontend dependencies
├── Dockerfile                # Production Dockerfile for Backend (Render / Hugging Face / VPS)
├── render.yaml               # Render Blueprint deployment specification
├── packages.txt              # Linux system libraries (cairo, pango, libffi for WeasyPrint)
└── .env.example              # Environment variables template
```

---

## 🚀 Deployment Guide (Recommended Split Architecture)

### Part 1: Deploy Backend (FastAPI) on Render or Hugging Face

#### Option A: Deploy on Render (via Docker or Blueprint)
1. Push this repository to your GitHub account.
2. Sign in to [Render](https://render.com) and click **New +** -> **Blueprint** (or **Web Service**).
3. Connect your GitHub repository `ATS_Score`.
4. If creating as a Web Service:
   - **Environment**: Docker (recommended for built-in WeasyPrint system libraries and CPU-optimized PyTorch)
   - **Health Check Path**: `/api/v1/health`
5. Under **Environment Variables**, add:
   - `SUPABASE_URL`: Your Supabase Project URL (`https://<project-ref>.supabase.co`)
   - `SUPABASE_KEY`: Your Supabase `service_role` secret key
   - `SUPABASE_ANON_KEY`: Your Supabase `anon` public key
   - `SUPABASE_JWT_SECRET`: Your Supabase JWT secret
   - `GROQ_API_KEY`: Your Groq API key from [Groq Console](https://console.groq.com/keys)
   - `ALLOWED_ORIGINS`: `https://*.streamlit.app,http://localhost:8501`
6. Click **Deploy**. Once deployed, copy your backend URL (e.g. `https://ai-resume-ats-backend.onrender.com`).

#### Option B: Deploy on Hugging Face Spaces (16GB RAM Free)
1. Go to [Hugging Face Spaces](https://huggingface.co/spaces) and click **Create new Space**.
2. Space SDK: Select **Docker** (Blank).
3. Connect your GitHub repository or push this repository to the Space.
4. Go to **Settings** -> **Variables and secrets**, and add the required environment variables (`SUPABASE_URL`, `SUPABASE_KEY`, `SUPABASE_ANON_KEY`, `SUPABASE_JWT_SECRET`, `GROQ_API_KEY`).
5. Hugging Face Spaces will build the `Dockerfile` and expose the API.

---

### Part 2: Deploy Frontend on Streamlit Community Cloud

1. Go to [share.streamlit.io](https://share.streamlit.io) and click **New app**.
2. Select your repository: `nsain04/ATS_Score`, branch: `main`.
3. Set **Main file path**: `frontend/streamlit_app.py`.
4. Click **Advanced settings** -> **Secrets**, and paste:
   ```toml
   BACKEND_URL = "https://your-backend-app.onrender.com"
   SUPABASE_URL = "https://your-project-id.supabase.co"
   SUPABASE_ANON_KEY = "your_supabase_anon_public_key"
   AUTH_REDIRECT_URL = "https://your-app-name.streamlit.app"
   ```
5. Click **Deploy**.

---

### Part 3: Configure Supabase Authentication

1. Go to your [Supabase Dashboard](https://supabase.com/dashboard) -> **Authentication** -> **URL Configuration**.
2. Add your deployed Streamlit URL (e.g. `https://your-app-name.streamlit.app`) to **Redirect URLs**.
3. If using Google OAuth:
   - Configure Google Client ID and Secret under **Authentication** -> **Providers** -> **Google**.
   - Set the Authorized Redirect URI in Google Cloud Console to `https://<your-project-ref>.supabase.co/auth/v1/callback`.

---

## 💻 Local Development

### 1. Clone & Set Up Virtual Environment
```bash
git clone https://github.com/nsain04/ATS_Score.git
cd ai-resume-ats
python -m venv venv
# Windows:
venv\Scripts\activate
# Linux/macOS:
source venv/bin/activate
```

### 2. Install Dependencies
```bash
pip install -r requirements.txt
python -m spacy download en_core_web_sm
```

### 3. Configure Environment Variables
Copy `.env.example` to `.env` and fill in your keys:
```bash
cp .env.example .env
```

### 4. Run Backend & Frontend
In Terminal 1 (Backend):
```bash
python -m uvicorn backend.main:app --reload --port 8000
```

In Terminal 2 (Frontend):
```bash
streamlit run frontend/streamlit_app.py
```
Open [http://localhost:8501](http://localhost:8501) in your browser.
