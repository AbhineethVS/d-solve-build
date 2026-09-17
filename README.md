# Oral Health Screening Widget (DSOLVE 2026 — PS1)

Two-minute oral health screening widget: capture **five** guided tooth photos → run a **team-trained** image classifier → show an instant visual report.

**Scope:** Screening only. Not a diagnosis. Anterior-focused phone photos.

---

## What we are building (core)

1. Guided capture of 5 images (frontal, upper, lower, left, right)
2. Backend inference with our fine-tuned EfficientNet-B0 checkpoint
3. Instant report: labels + confidence + “consider a dental visit / no obvious visual concern”

**Parked until core works:** 3D tooth pins, chatbot, clinic locator, Flutter, auth, database, wear classifier without data.

---

## Tech stack (locked)

| Layer | Choice |
|---|---|
| Train | Python 3.11, PyTorch, `timm`, Kaggle GPU |
| Model | EfficientNet-B0 (ImageNet → fine-tune) |
| API | FastAPI + Uvicorn |
| App | Vite + React + TypeScript + Tailwind |
| Data | Session only — no DB for MVP |

---

## Repo layout (target)

```
/train     dataset prep + training scripts + metrics plots
/api       POST /analyze — loads best.pt
/web       5-shot capture UI + report screen
/docs      plan, architecture, datasets, runbooks
/weights   best.pt (or release link — keep repo lean)
```

---

## Quick start (after scaffold)

```bash
# API
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

Training: see [`docs/DATASETS.md`](docs/DATASETS.md) and [`train/README.md`](train/README.md) (added with scaffold).

---

## Rules we care about

- Build during the 36-hour window. No forking pre-built dental screening products.
- Public datasets, frameworks, and pretrained backbones are allowed.
- We must explain architecture, training, and limitations in Q&A.

---

## Docs

| Doc | Purpose |
|---|---|
| [`docs/PLAN.md`](docs/PLAN.md) | Locked MVP + cut list + pitch |
| [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) | Pipeline, API contract, folders |
| [`docs/DATASETS.md`](docs/DATASETS.md) | What we train on and why |
| [`docs/CHECKLIST.md`](docs/CHECKLIST.md) | 36-hour checkpoints |
| [`docs/DECISIONS.md`](docs/DECISIONS.md) | Living decision log — agents must update |
| [`docs/SKILLS.md`](docs/SKILLS.md) | Agent skills installed for this repo |

---

## Disclaimer

This tool is a **visual screening aid** for educational / hackathon demonstration. It is not medical advice and does not replace a dentist.
