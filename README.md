# Math Solver

A Streamlit app that solves math problems step by step. Type a problem or upload a photo or screenshot of one, and Google's Gemini Flash model returns the working and a final answer.

## Features

- Text input or image upload (PNG, JPG, WebP; up to 5 MB)
- Phone photos are rotated correctly and large images are downscaled automatically
- Math is rendered as LaTeX
- Final answer is shown separately from the working
- Download the solution as Markdown
- Automatic retries on temporary Gemini errors (rate limits, overload)

## Requirements

- Python 3.10 or newer
- A Gemini API key (free to create at <https://aistudio.google.com/apikey>)

## Run locally

```bash
# 1. Create and activate a virtual environment
python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate

# 2. Install dependencies
pip install -r requirements.txt

# 3. Provide your API key (pick one)
export GEMINI_API_KEY="your-key"   # Windows PowerShell: $env:GEMINI_API_KEY="your-key"
#   or create .streamlit/secrets.toml containing:  GEMINI_API_KEY = "your-key"
#   or just paste the key into the app's sidebar after it starts

# 4. Start the app
streamlit run app.py
```

The app opens at <http://localhost:8501>.

## Configuration

| Setting | Where | Default |
|---|---|---|
| `GEMINI_API_KEY` | environment variable or Streamlit secrets | none (sidebar field appears if missing) |
| `GEMINI_MODEL` | environment variable, Streamlit secrets, or the sidebar | `gemini-flash-latest` |

`gemini-flash-latest` is a rolling alias for the current Flash model. Google renames and retires model versions regularly, so if you want stable behavior, pin a specific model name from <https://ai.google.dev/gemini-api/docs/models>. If you see "model not found", the name is wrong or retired.

## Push to GitHub

1. Create a `.gitignore` so your key never gets committed:

   ```bash
   printf ".venv/\n__pycache__/\n.env\n.streamlit/secrets.toml\n" > .gitignore
   ```

2. Initialize the repository and commit:

   ```bash
   git init
   git add app.py README.md requirements.txt .gitignore
   git commit -m "Initial commit: Math Solver"
   ```

3. On <https://github.com/new>, create a new repository named `math-solver`. Leave "Add a README" and ".gitignore" **unchecked** (you already have them).

4. Connect and push (replace `YOUR-USERNAME`):

   ```bash
   git branch -M main
   git remote add origin https://github.com/YOUR-USERNAME/math-solver.git
   git push -u origin main
   ```

   GitHub no longer accepts account passwords for `git push`. When prompted, use a personal access token, or sign in with the GitHub CLI (`gh auth login`) or SSH.

   With the GitHub CLI installed, steps 3 and 4 can be a single command: `gh repo create math-solver --public --source=. --push`

Before pushing, run `git status` and confirm that no secrets file is listed.

## Deploy on Streamlit Community Cloud (free)

1. Go to <https://share.streamlit.io> and sign in with GitHub.
2. Click **Create app** and choose your `math-solver` repository, branch `main`, and main file `app.py`.
3. Open **Advanced settings**:
   - Pick Python 3.11 or 3.12.
   - In **Secrets**, paste:

     ```toml
     GEMINI_API_KEY = "your-key"
     ```

4. Click **Deploy**. The first build takes a few minutes. You get a public `*.streamlit.app` URL.

To update the app, push new commits to `main`; it redeploys automatically.

### Before sharing the URL publicly

If you store your key in Secrets, **everyone who opens the app uses your quota**. Options:

- Don't set the secret. Visitors then enter their own key in the sidebar.
- Make the app private (Streamlit app sharing settings) and invite specific people.
- Set quota limits for your key in Google AI Studio / Google Cloud.

Also review Google's current Gemini API terms for how content sent on the free tier may be used, especially if students upload personal photos.

## Testing checklist

Verify these once after setting up, using a real key:

- [ ] Type `Solve 2x^2 - 5x - 3 = 0`. Expect steps and a final answer of x = 3 and x = -1/2.
- [ ] Upload a clear photo of a printed problem.
- [ ] Upload a handwritten problem.
- [ ] Upload text plus an image together.
- [ ] Click Solve with empty input (expect a warning).
- [ ] Enter a wrong key (expect "rejected the API key").
- [ ] Click **Download solution** and confirm the result stays on screen.
- [ ] Check that fractions, roots and integrals render properly.

## Limitations

- AI answers can be wrong, especially on long multi-step or very advanced problems. Check important results.
- Handwriting and low-quality photos may be misread; the model is instructed to say what it couldn't read rather than guess.
- Each solve is independent; there is no follow-up chat.
