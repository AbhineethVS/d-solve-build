# Implementation plan — Frontend + Backend (together)

**Audience:** FE + BE pair (work in unison). Trainer and Wow stay parallel — see notes per phase.  
**Sources of truth:** `docs/DESIGN.md` (layout) · `docs/ARCHITECTURE.md` (API) · `docs/PLAN.md` (hybrid)  
**Role checklists:** `docs/guides/FRONTEND.md` · `docs/guides/BACKEND.md`

Orchestrator: keep both on the same phase exit criteria before starting the next phase.

---

## Phase overview

| Phase | Name | Goal | Exit criteria |
|---|---|---|---|
| **0** | Contract & scaffold | Empty apps + agreed JSON | FE and BE run locally; fixture JSON shared |
| **1** | Dummy loop | 5 photos → dummy report | Full UI flow on phone/laptop with fake findings |
| **2** | Real capture polish | Reliable camera + upload | Five real blobs; errors handled |
| **3** | CNN integrate | `best.pt` in `/analyze` | Discoloration/other from model when weights present |
| **4** | Vision + fusion | Crooked/wear + merge | PS1 three concerns in one report |
| **5** | Hardening & demo | Stable live path | Mentor/demo ready; README run steps work |
| **6** | Wow hook (optional) | Evidence/map slot | Does not break phases 0–5 |

Trainer runs **from Phase 0 in parallel** (GPU). Do not block Phase 1–2 on `best.pt`.

```
Phase 0 ──► 1 ──► 2 ──► 3 ──► 4 ──► 5
                │         ▲     ▲
                │         │     └── needs API key
                │         └── needs best.pt (or skip to 4 vision-only)
                └── Trainer parallel ─────────────────┘
Phase 6 (Wow) after Phase 1 fixture exists ───────────►
```

---

## Phase 0 — Contract & scaffold

**Timebox:** 1–2 hours · **Together:** yes

### Together first (15–30 min)

- [ ] Re-read `DESIGN.md` + `ARCHITECTURE.md` example JSON  
- [ ] Freeze multipart fields: `frontal`, `upper`, `lower`, `left`, `right`  
- [ ] Freeze response: `overall`, `disclaimer`, `views[]`, `summary[]`, `source`  
- [ ] Write `docs/fixtures/analyze_dummy.json` (or paste in chat) for FE  

### Backend

- [ ] Create `api/` — venv, `requirements.txt`, FastAPI app  
- [ ] `GET /health`  
- [ ] `POST /analyze` accepts 5 files, returns **dummy** JSON (ignore image bytes)  
- [ ] CORS for Vite (`localhost:5173`)  
- [ ] `.env.example` without secrets  

### Frontend

- [ ] Create `web/` — Vite React TS + Tailwind  
- [ ] `VITE_API_URL`  
- [ ] Screen state machine: `landing | capture | analyzing | report`  
- [ ] Empty shells matching DESIGN wireframes (no camera yet OK)  

### Exit

- [ ] `uvicorn` + `npm run dev` both up  
- [ ] Browser `GET /health` OK  
- [ ] FE can `POST` and log dummy JSON (even from a temp button)

**Trainer (parallel):** T1–T3 data + GPU notebook started.

---

## Phase 1 — Dummy end-to-end loop

**Timebox:** 2–4 hours · **Together:** hourly sync

### Backend

- [ ] Pydantic schemas match ARCHITECTURE  
- [ ] Dummy findings include at least one of each: crooked, wear, discoloration (so UI can be built)  
- [ ] Always return `disclaimer`  
- [ ] Reject clearly if a file field missing  

### Frontend

- [ ] Landing (Start + disclaimer) per DESIGN  
- [ ] Capture steps 1–5 with **placeholders** (upload file per view counts)  
- [ ] Analyzing screen while waiting  
- [ ] Report: overall banner, summary list (PS1 order), five thumbs, Start over  
- [ ] Wire real `FormData` POST to dummy API  

### Exit

- [ ] One person completes Landing → 5 uploads → Report without console errors  
- [ ] Report shows sources if present  

**Wow:** may start W1 against dummy JSON.

---

## Phase 2 — Capture that works on a phone

**Timebox:** 2–4 hours

### Frontend (lead) · Backend (support)

- [ ] `getUserMedia` + Capture to blob (manual shutter)  
- [ ] Retake / Next / progress `n of 5`  
- [ ] Tips per view (frontal, upper, lower, left, right)  
- [ ] Guide overlay (simple silhouette OK)  
- [ ] Gallery fallback if permission denied  
- [ ] BE: CORS for LAN IP / tunnel origin FE will use  
- [ ] BE: bind `0.0.0.0:8000`  

### Exit

- [ ] Real phone (or tight laptop webcam) produces 5 images and a dummy report  

---

## Phase 3 — CNN path

**Timebox:** 2–4 hours after `best.pt` exists  
**Skip temporarily** if weights late → jump to Phase 4 vision-only, return here later.

### Backend

- [ ] Load `weights/best.pt` + `class_map.json` in lifespan  
- [ ] Preprocess 224×224, ImageNet norm  
- [ ] Per-view softmax; map Tooth Discoloration → `discoloration`; others → `other_visual_concern`  
- [ ] Threshold (default 0.55)  
- [ ] `/health.model_loaded` accurate  
- [ ] If no weights: keep dummy/CNN-skip without crashing  

### Frontend

- [ ] No structural change — show whatever `summary` returns  
- [ ] Plain-language labels (not raw class dumps as the only text)  

### Exit

- [ ] With weights: real discoloration/other tags appear  
- [ ] Without weights: Phase 1 behavior still works  

**Trainer:** deliver `best.pt` + class map + metrics to BE (T6).

---

## Phase 4 — Vision API + fusion

**Timebox:** 3–5 hours · **Needs:** API key in `api/.env` (not committed)

### Backend

- [ ] `VISION_PROVIDER` + key  
- [ ] Vision call with all 5 images; strict JSON for crooked + wear (+ optional colour)  
- [ ] Parallel: CNN + vision  
- [ ] Fusion rules (PLAN/ARCHITECTURE):  
  - discoloration → prefer CNN if confident  
  - crooked, wear → vision  
  - overall from PS1 concerns  
- [ ] Graceful degrade: vision down → CNN-only + note; CNN down → vision-only  

### Frontend

- [ ] Optional quiet `source` badges  
- [ ] Analyzing copy mentions colour / alignment / wear  
- [ ] Handle longer latency (timeout messaging)  

### Exit

- [ ] Live report can show crooked and/or wear from vision  
- [ ] Discoloration from CNN when model loaded  
- [ ] One fused JSON — FE does not merge  

---

## Phase 5 — Hardening & demo

**Timebox:** 2–3 hours

### Together

- [ ] Error states: camera, network, analyze 4xx/5xx  
- [ ] File size limits; sensible timeouts  
- [ ] README: how to run FE+BE, env vars, where weights go  
- [ ] Dry-run demo script (3–5 min)  
- [ ] Backup: screen recording + sample images if Wi‑Fi dies  
- [ ] Confirm no secrets in git  

### Exit

- [ ] Teammate can clone/follow README and hit a report  
- [ ] Orchestrator signs off “demo path green”  

---

## Phase 6 — Wow integration (optional)

**Only if Phase 1+ green.** See `docs/guides/WOW.md`.

- [ ] FE leaves a Report slot for evidence / map  
- [ ] Wow PRs into `web/`; pair reviews  
- [ ] Phone layout still works  

---

## Sync ritual (every phase)

| Cadence | Action |
|---|---|
| Start of phase | Orchestrator reads exit criteria aloud |
| ~hourly | FE+BE: “contract still match?” |
| End of phase | Check exit boxes; only then advance |
| Any API shape change | Edit ARCHITECTURE + fixture the same hour |

---

## Parallel tracks (not FE/BE blockers)

| Track | Owner | Aligns with |
|---|---|---|
| Training `best.pt` | Train | Phase 3 |
| Pitch video / README polish | All (endgame) | After Phase 5 |
| Wow evidence/map | Wow | Phase 6 |

---

## Do not do mid-phases

- New screens (auth, chat, maps)  
- Auto-capture shutter as a Phase 1–4 dependency  
- Redesigning JSON every hour  
- Waiting on Wow or perfect F1 to start Phase 1  
