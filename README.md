# Oral Health Screening Widget (DSOLVE 2026 — PS1)

Two-minute oral health screening widget: capture **five** guided tooth photos → **hybrid analysis** (team-trained CNN + vision API) → instant visual report for **crooked teeth, wear, and discoloration**.

**Scope:** Screening only. Not a diagnosis. Anterior-focused phone photos.

---

## What we are building (core)

1. Guided capture of 5 images (frontal, upper, lower, left, right)
2. **EfficientNet-B0** we fine-tune on Oral Diseases → discoloration (+ other visual concerns)
3. **Vision API** (GPT-4o or Gemini) → crooked teeth + tooth wear
4. FastAPI **fuses** both into one report with confidence + source
5. Overall: “consider a dental visit” / “no obvious visual concern”

**Parked until core works:** 3D tooth pins, chatbot, clinic locator, Flutter, auth, database.

---

## Tech stack (locked)

| Layer | Choice |
|---|---|
| Train | PyTorch + `timm`, Kaggle Notebook or Colab GPU |
| CNN | EfficientNet-B0 on [Oral Diseases](https://www.kaggle.com/datasets/salmansajid05/oral-diseases) |
| Vision API | GPT-4o **or** Gemini Flash (env-selected) |
| API | FastAPI + Uvicorn (CNN + vision + fusion) |
| App | Vite + React + TypeScript + Tailwind |
| Data | Session only — no DB for MVP |

---

## Repo layout (target)

```
/train     training scripts + metrics
/api       POST /analyze — CNN + vision API + fusion
/web       5-shot capture UI + report screen
/docs      plan, architecture, datasets, decisions
/weights   best.pt (gitignored / release link)
```

---

## Quick start (after scaffold)

```bash
# API — set OPENAI_API_KEY or GEMINI_API_KEY in api/.env
cd api
python -m venv .venv
.\.venv\Scripts\activate   # Windows
pip install -r requirements.txt
uvicorn main:app --reload --host 0.0.0.0 --port 8000

# Web
cd web
npm install
npm run dev
```

See [`docs/PLAN.md`](docs/PLAN.md), [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md), [`docs/DATASETS.md`](docs/DATASETS.md).

---

## Rules we care about

- Build during the 36-hour window. No forking pre-built dental screening products.
- Public datasets, frameworks, and pretrained AI models / APIs are allowed.
- We must explain architecture, training, hybrid fusion, and limitations in Q&A.

---

## Docs

| Doc | Purpose |
|---|---|
| [`docs/PLAN.md`](docs/PLAN.md) | Locked MVP + hybrid mapping + cut list |
| [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) | Pipeline, API contract, fusion |
| [`docs/DATASETS.md`](docs/DATASETS.md) | What we train on and why |
| [`docs/CHECKLIST.md`](docs/CHECKLIST.md) | 36-hour checkpoints |
| [`docs/DECISIONS.md`](docs/DECISIONS.md) | Living decision log — agents must update |
| [`docs/DESIGN.md`](docs/DESIGN.md) | Layout & UX wireframes (colours free) |
| [`docs/TEAM.md`](docs/TEAM.md) | FE+BE pair, Train, Wow |
| [`docs/guides/`](docs/guides/) | Step-by-step role guides |
| [`docs/SKILLS.md`](docs/SKILLS.md) | Agent skills |

---

## Disclaimer

This tool is a **visual screening aid** for educational / hackathon demonstration. It is not medical advice and does not replace a dentist.
