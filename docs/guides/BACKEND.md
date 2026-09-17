# Backend guide (BE)

You own **`api/`**. You work **in unison with Frontend** — ship a stable contract early, then deepen hybrid intelligence behind it.

**Stack:** FastAPI + Uvicorn · CNN EfficientNet-B0 · Vision API (GPT-4o or Gemini) · Skill: `fastapi-screening`  
**Phases:** follow **`docs/IMPLEMENTATION.md`** with FE.

---

## Your job in one line

`POST /analyze` accepts five images and returns one fused screening report (PS1 concerns + `source` + overall).

---

## Before you code

1. Read `docs/ARCHITECTURE.md` and **`docs/IMPLEMENTATION.md`** — that JSON + phase exit criteria are the contract.  
2. Sit with FE: agree field names and response shape; paste a **fixture** they can use immediately.  
3. Create `api/.env` (gitignored): keys later; `CORS_ORIGINS` for Vite (`http://localhost:5173`, phone LAN origin).  
4. Sync with FE hourly; sync with Train when `best.pt` is ready; sync with Wow on JSON only.

---

## Step-by-step build order

### Step B1 — Skeleton (1 hr)

- [ ] `api/` with venv + `requirements.txt`  
- [ ] `GET /health`  
- [ ] `POST /analyze` accepting five multipart files  
- [ ] Return **hard-coded / dummy** JSON matching ARCHITECTURE  
- [ ] CORS enabled for FE  

**Done when:** FE can hit analyze from the browser.

### Step B2 — Schemas (30–60 min)

- [ ] Pydantic models for `views`, `summary`, `overall`, `disclaimer`, `source`  
- [ ] Validate concern enums: `discoloration | crooked | wear | other_visual_concern`  

**Done when:** invalid shapes fail loudly in dev.

### Step B3 — Image safety (30 min)

- [ ] Accept jpeg/png/webp  
- [ ] Reject oversized files (e.g. >8MB)  
- [ ] Never log raw bytes  

### Step B4 — CNN path (2–4 hrs) — when Train delivers `best.pt`

- [ ] Load model once in lifespan  
- [ ] Preprocess 224×224 ImageNet norm  
- [ ] Softmax per view; map classes via `class_map.json`  
- [ ] Threshold (default 0.55)  
- [ ] If weights missing → skip CNN, set `model_loaded: false`  

**Done when:** real discoloration / other tags appear in `views`.

### Step B5 — Vision API path (2–4 hrs)

- [ ] Env: `VISION_PROVIDER=openai|gemini` + API key  
- [ ] One call with all five images + **strict JSON schema** for crooked, wear, optional colour  
- [ ] Screening language only; low temperature  
- [ ] Timeouts + clear error if key missing  

**Done when:** `summary` can include `crooked` / `wear` with `source: vision_api`.

### Step B6 — Fusion (1–2 hrs) — **critical**

- [ ] Run CNN + vision **in parallel**  
- [ ] Discoloration: prefer CNN if confident; else vision  
- [ ] Crooked + wear: vision only  
- [ ] Other CNN classes → `other_visual_concern`  
- [ ] Overall: any strong PS1 concern → `consider_visit`  
- [ ] Always disclaimer  

**Done when:** one coherent report; FE does not merge anything.

### Step B7 — Demo ops (1 hr)

- [ ] Bind `0.0.0.0:8000` for phone  
- [ ] Optional tunnel if campus Wi‑Fi blocks LAN  
- [ ] `/health` shows model + vision configured flags  

---

## When (timeline)

| Window | You should be on |
|---|---|
| Tonight | B1–B3 + dummy for FE |
| Overnight / Fri AM | B4 as soon as `best.pt` exists |
| Fri | B5–B6 hybrid live |
| Endgame | Stability only; help FE/Wow |

---

## Sync rules with Frontend

- Dummy first — never block FE on training  
- Contract change = joint edit to ARCHITECTURE  
- Tell FE the exact LAN URL for phone tests  

## Sync with Train

- Expected paths: `weights/best.pt`, `weights/class_map.json`  
- Need class index order and metrics blurb for README  

## Sync with Wow

- Export a sample successful JSON; Wow must not require new endpoints unless pair agrees  

---

## Out of scope

Building React screens, training loops, chatbots, databases.
