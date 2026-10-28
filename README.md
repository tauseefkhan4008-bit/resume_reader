# 📄 ATS Resume Analyzer

Upload a resume (PDF, DOCX or TXT) and get an **ATS score out of 100** plus specific, prioritised improvements. Built with **Streamlit** and **Google Gemini Flash**.

## Features

- Upload PDF / DOCX / TXT resumes (up to 5 MB)
- Overall ATS score with a breakdown across 6 weighted categories
- Prioritised improvements (High / Medium / Low) with before/after style examples
- Optional job description for targeted keyword matching
- Found vs. missing keywords
- Local "what the ATS sees" check: extracted text, contact details, detected sections
- Download the full report as Markdown
- Pick the Gemini model from the sidebar (or type any model name)

## How the score works

Gemini rates each category from 0 to 100, and the app computes the overall score itself as a weighted average, so the maths is consistent and transparent:

| Category | Weight |
|---|---|
| Keywords & Relevance | 25% |
| Experience & Achievements | 25% |
| Formatting & ATS Parsability | 20% |
| Skills Section | 10% |
| Contact Information | 10% |
| Clarity, Grammar & Length | 10% |

> The score is an AI-based estimate, not the output of any specific commercial ATS. Use it as guidance for improving your resume, not as a guarantee.

## Project structure

```
.
├── app.py             # the whole Streamlit app
├── requirements.txt   # Python dependencies
└── README.md
```

## Run locally

1. **Get a free Gemini API key** at <https://aistudio.google.com/apikey>.

2. **Create a virtual environment and install dependencies** (Python 3.10+):

   ```bash
   python -m venv venv
   source venv/bin/activate        # Windows: venv\Scripts\activate
   pip install -r requirements.txt
   ```

3. **Provide your API key**, using either option:

   - Paste it in the app's sidebar, or
   - Set it once as an environment variable / secret:

     ```bash
     # macOS / Linux
     export GEMINI_API_KEY="your-key"
     # Windows PowerShell
     $env:GEMINI_API_KEY="your-key"
     ```

     or create `.streamlit/secrets.toml`:

     ```toml
     GEMINI_API_KEY = "your-key"
     ```

4. **Start the app:**

   ```bash
   streamlit run app.py
   ```

   It opens at <http://localhost:8501>.

## Push to GitHub

First create an **empty** repository on GitHub (no README/.gitignore) via **New repository**. Then, inside your project folder:

```bash
# 1. Make sure secrets and the venv are never committed
cat > .gitignore <<'EOF'
venv/
.venv/
__pycache__/
*.pyc
.env
.streamlit/secrets.toml
EOF

# 2. Initialise and commit
git init
git add .
git commit -m "Initial commit: ATS resume analyzer"
git branch -M main

# 3. Connect to GitHub and push (replace with your own URL)
git remote add origin https://github.com/<your-username>/<your-repo>.git
git push -u origin main
```

If Git asks for a password, use a **Personal Access Token** (GitHub → Settings → Developer settings → Personal access tokens) or sign in with GitHub CLI (`gh auth login`).

> ⚠️ **Never commit your API key.** If you accidentally do, revoke it in Google AI Studio and create a new one.

## Deploy on Streamlit Community Cloud (free)

1. Go to <https://share.streamlit.io> and sign in with GitHub.
2. Click **Create app** → **Deploy a public app from GitHub**.
3. Choose your **repository**, **branch** (`main`) and set **Main file path** to `app.py`.
4. Open **Advanced settings → Secrets** and paste:

   ```toml
   GEMINI_API_KEY = "your-key"
   ```

   (Optional: choose a Python version, 3.11 or 3.12 recommended.)
5. Click **Deploy**. The first build takes a couple of minutes; you'll get a public `*.streamlit.app` URL.

To update the live app, just `git push` to the same branch; it redeploys automatically. To change the secret later: app **⋮ menu → Settings → Secrets**.

> **Public app + your key = your quota.** If the app is public and you put your key in Secrets, anyone using it spends your Gemini quota. Either keep the app private (share only with viewers you choose), or remove the secret so visitors enter their own key in the sidebar.

## Choosing a model

The sidebar offers `gemini-2.5-flash` (default), `gemini-2.5-flash-lite` and `gemini-2.0-flash`. Choose **Custom...** to type any other Gemini model code (for example a newer Flash release). Google retires older models over time, so check <https://ai.google.dev/gemini-api/docs/models> for current names. You can also change `DEFAULT_MODEL` at the top of `app.py`.

## Troubleshooting

| Problem | Fix |
|---|---|
| "Very little text could be extracted" | Your PDF is probably a scan or image-based. Export a text-based PDF/DOCX from Word or Google Docs. (An ATS can't read it either.) |
| "API key rejected" | Check the key, and make sure the Gemini API is enabled for your Google project. |
| "Model not found" | The model name is wrong or retired. Pick another model or use a custom name. |
| "Rate limit / quota reached" | Free-tier limits hit. Wait a minute or switch to a lighter model like `gemini-2.5-flash-lite`. |
| `ModuleNotFoundError` on Streamlit Cloud | Make sure `requirements.txt` is in the repo root and committed. |

## Privacy

The text of your resume (and optional job description) is sent to Google's Gemini API for analysis. The app itself doesn't store files or results beyond your current browser session. Review Google's Gemini API terms for how they handle data, and avoid uploading documents you aren't comfortable sharing.

## Tech stack

Python · Streamlit · Google Gemini (`google-genai` SDK) · Pydantic · pypdf · python-docx
