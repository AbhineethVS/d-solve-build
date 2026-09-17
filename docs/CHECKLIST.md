# 36-hour checklist

Hackathon: **Thu 6:00 PM → Sat 6:00 AM code freeze** (see event brochure).

---

## Before coding (now)

- [x] Problem = PS1  
- [x] Core stack locked (PyTorch train → FastAPI → React)  
- [x] Docs written (`README`, `PLAN`, `ARCHITECTURE`, `DATASETS`)  
- [ ] Team agrees on cut list (no extras before core)  
- [ ] Kaggle account ready + dataset accepted  
- [x] GitHub repo created (public, per rules)  
- [x] Who owns what decided — see `docs/TEAM.md` (all 4 code: P1 api, P2 train, P3 capture, P4 report)  

---

## Interim 1 — ~Thu 7:00 PM (ideation / stack)

- [ ] One-sentence pitch ready for mentors  
- [ ] Show this docs folder / architecture diagram  
- [ ] Confirm: training on Kaggle, demo on laptop CPU  
- [ ] Scaffold started: empty `train/`, `api/`, `web/`  

---

## Night 1 — pipeline skeleton

- [ ] Kaggle: data downloaded, folders mapped to classes  
- [ ] Training job launched (or queued)  
- [ ] FastAPI: `/health` + `/analyze` with **dummy** predictions  
- [ ] Web: 5 capture steps + wired to API (even if dummy)  
- [ ] CORS works phone ↔ laptop  

---

## Interim 2 — ~Fri 10:30 AM (core functionality)

- [ ] Real `best.pt` loaded **or** clear ETA + dummy demo still works  
- [ ] End-to-end: 5 photos → JSON → report UI  
- [ ] Val metrics written down for Q&A  
- [ ] Mentor feedback noted; only change scope if blocked  

---

## Afternoon / evening Fri

- [ ] Swap dummy → real model  
- [ ] Threshold tuned; bad cases collected for honesty in pitch  
- [ ] README: setup + disclaimer + metrics  
- [ ] Pitch video filmed/edited (must be within 36h)  
- [ ] Social post planned (tags per event rules)  

---

## Interim 3 — ~Fri 6:30 PM

- [ ] Full loop on teammate phone  
- [ ] Fallback: recorded screen capture if Wi-Fi dies  
- [ ] Slide outline / demo script (3–5 min)  

---

## Code freeze — Sat 6:00 AM

- [ ] Repo public, clean, runnable  
- [ ] No secrets in git  
- [ ] Weights available (in repo / release / Drive link in README)  
- [ ] Pitch video posted if required before freeze  

---

## Demo script (keep under 5 minutes)

1. Problem: people delay dental visits; need a 2-minute visual check  
2. Capture five photos live  
3. Show report + confidence  
4. “We trained EfficientNet-B0 on Kaggle Oral Diseases — here’s F1”  
5. Limitations: screening only; wear/crooked not in v1 checkpoint; phone ≠ clinic  
6. Next: evidence crops / 3D pins (if built) or roadmap  

---

## Kill switches

| If this happens | Do this |
|---|---|
| Training not done by Fri noon | Keep dummy labels for UI; show notebook progress + partial checkpoint |
| Laptop can’t load model | Export TorchScript/smaller batch; or CPU-only smaller input |
| Camera fails on venue Wi-Fi | Upload-from-gallery path + prerecorded demo |
| Tempted to add chatbot/3D early | Re-read `PLAN.md` cut list |
