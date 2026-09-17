# Plan — locked MVP

**Event:** DSOLVE 2026 (PS1 — Oral Health Screening Widget)  
**Status:** Core plan locked — **hybrid analysis** (trained CNN + vision API).

---

## Problem (from brief)

Build a free ~2-minute widget (web or phone) that:

- Guides the patient through prompts
- Captures **five** quick tooth images
- Analyses them and shows an instant visual report
- Highlights concerns such as **crooked teeth, tooth wear, discoloration**
- Helps the user decide whether to see a dentist

---

## Our product story (what we lead with)

> Five guided phone photos → **hybrid screening**: a model **we trained** for discoloration (and related visible conditions) **plus** a vision API (GPT/Gemini) for crooked teeth and wear — fused into one patient-friendly report. Screening only; not a diagnosis.

Later differentiators (after core): 3D anterior pins, evidence crops, anterior-only honesty.

---

## Core MVP (must ship)

| # | Feature | Done when |
|---|---|---|
| 1 | Guided 5-photo capture | User can take/upload frontal, upper, lower, left, right |
| 2 | Trained classifier | EfficientNet-B0 on Oral Diseases; `best.pt` (discoloration + other) |
| 3 | Vision API path | GPT or Gemini analyzes same 5 photos for **crooked** + **wear** (+ optional colour confirm) |
| 4 | Fusion in `/analyze` | One JSON report with PS1 buckets + sources (`trained_model` / `vision_api`) |
| 5 | Report UI | 5 photos + findings for crooked / wear / discoloration + overall recommendation |
| 6 | README + setup | Judges can run demo; API key via `.env` (not committed) |
| 7 | Pitch video | >30s, English, posted per event rules |

---

## Explicitly cut until core is green

- 3D jaw / FDI tooth pins  
- Chatbot, clinic map, auth, Postgres  
- Flutter / second app  
- Training a wear or crooked CNN (no solid labels — use vision API instead)  
- Multi-model tooth/gum/occlusion stack  

---

## Who detects what (hybrid)

| PS1 concern | Primary source | Fallback |
|---|---|---|
| **Discoloration** | Trained EfficientNet-B0 | Vision API if model missing / low conf |
| **Crooked teeth** | Vision API | — |
| **Tooth wear** | Vision API | — |
| Other (calculus, caries, gingivitis, …) | Trained model → `other_visual_concern` | Optional; don’t lead the pitch |

**Fusion rules:**

1. Run CNN + vision API **in parallel** on the five images.  
2. Report always structured around the **three PS1 concerns** first.  
3. Discoloration: prefer CNN if confidence ≥ threshold; else vision API.  
4. Crooked / wear: vision API only.  
5. Any high-confidence PS1 concern → `consider_visit`; else `no_obvious_concern`.  
6. Each finding includes `source`: `trained_model` | `vision_api`.

Always show disclaimer: screening ≠ diagnosis.

---

## Team split (4 people — all coding)

See **[`docs/TEAM.md`](TEAM.md)**. Pitch/demo is **endgame**, not a dedicated role now.

| Code | Role | Owns |
|---|---|---|
| **P1 (You)** | Lead / API | `api/`, CNN load, vision API client, **fusion**, merges |
| **P2** | Training | Kaggle/Colab train, `best.pt`, metrics |
| **P3** | Capture UI | `web/` guided 5-photo flow + camera |
| **P4** | Report UI | findings by PS1 concern + source badges + API client |

---

## Success criteria for “core ready”

1. Teammate’s mouth (or samples) → 5 photos → report covering crooked / wear / discoloration where visible  
2. Demo works if CNN missing (vision-only) **or** if API down (CNN + honest gaps) — prefer both up  
3. Explain: dataset, F1, why hybrid, limitations  
4. No forked dental product repos  

---

## After core (priority order)

1. Evidence crop / highlight on the source photo  
2. Anterior-only copy + tooth-number hints by view  
3. 3D pinned viewer  
4. Polish only  
