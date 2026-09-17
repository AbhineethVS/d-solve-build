# Plan — locked MVP

**Event:** DSOLVE 2026 (PS1 — Oral Health Screening Widget)  
**Status:** Core plan locked. Extras only after end-to-end pipeline works.

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

Not “generic AI that screens teeth.”

> Five guided phone photos → a model **we trained** on public oral-disease images → clear findings with confidence and an honest “consider a visit” outcome. Screening only; phone photos, not a clinic exam.

Later differentiators (after core): 3D anterior pins, evidence crops, anterior-only honesty.

---

## Core MVP (must ship)

| # | Feature | Done when |
|---|---|---|
| 1 | Guided 5-photo capture | User can take/upload frontal, upper, lower, left, right |
| 2 | Trained classifier | EfficientNet-B0 fine-tuned on Oral Diseases (Kaggle); `best.pt` saved |
| 3 | Inference API | `POST /analyze` returns labels + confidence per image |
| 4 | Report UI | Shows 5 photos + findings list + overall screening recommendation |
| 5 | README + setup | Judges can understand and run the demo |
| 6 | Pitch video | >30s, English, posted per event rules |

---

## Explicitly cut until core is green

- 3D jaw / FDI tooth pins  
- Fine-tune-vs-zero-shot dual path / Gemini as primary  
- Chatbot, clinic map, auth, Postgres  
- Flutter / second app  
- Wear as a trained class (no solid public photo set — say so in pitch)  
- Disease taxonomy beyond what the checkpoint supports  

---

## Model ↔ PS1 mapping

**Train first:** multi-class on Oral Diseases (includes **Tooth Discoloration** + related visible conditions).

| Model class | Report language |
|---|---|
| Tooth Discoloration | Discoloration / staining |
| Calculus, Caries, Gingivitis, Ulcer, Hypodontia | “Other visual concern” (data-backed bonus) |
| Crooked / crowding | Phase 2 model **only if** primary checkpoint is done |
| Wear | Not trained — disclosed limitation |

Overall score:

- High-confidence concern on any view → **Consider a dental visit**
- Else → **No obvious visual concern from these photos**

Always show disclaimer: screening ≠ diagnosis.

---

## Team split (4 people — all coding)

See **[`docs/TEAM.md`](TEAM.md)**. Pitch/demo is **endgame**, not a dedicated role now.

| Code | Role | Owns |
|---|---|---|
| **P1 (You)** | Lead / API | `api/`, model load, merges, integration |
| **P2** | Training | `train/`, Kaggle, `best.pt`, metrics |
| **P3** | Capture UI | `web/` guided 5-photo flow + camera |
| **P4** | Report UI | `web/` analyzing + report + API client |

---

## Success criteria for “core ready”

1. Teammate’s mouth (or curated sample set) → 5 photos → report in &lt;30s  
2. Checkpoint runs on laptop CPU for the live demo  
3. Someone can explain: dataset, split, architecture, val F1/accuracy, failure modes  
4. No dependency on forked dental product repos  

---

## After core (priority order)

1. Evidence crop / highlight on the source photo  
2. Anterior-only copy + tooth-number hints by view  
3. Optional second model for crowding/crooked  
4. 3D pinned viewer  
5. Polish only  
