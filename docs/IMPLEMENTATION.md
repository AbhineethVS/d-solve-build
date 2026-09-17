# Codex prompts — FE + BE on one computer

**How to use:** Copy **one prompt at a time** into Codex. Wait until that phase’s **Done when** is true before pasting the next.  
**Setup:** Frontend and backend share **one PC**, one repo (`d-solve-build`), `api/` + `web/` side by side. Prefer `localhost`.

**Always open in the agent’s context (or @-mention):**

- `docs/DESIGN.md` — layout only (no fixed colours)  
- `docs/ARCHITECTURE.md` — API JSON contract  
- `docs/PLAN.md` — hybrid CNN + vision API  

**Do not** paste Phase N+1 early. **Do not** start Wow or training inside these prompts (trainer uses `docs/guides/TRAINER.md` separately).

---

## Prompt 0 — Scaffold both apps on this machine

```
You are implementing our DSOLVE oral screening widget on a SINGLE computer (FE+BE together in one repo).

Read first:
- docs/DESIGN.md
- docs/ARCHITECTURE.md
- docs/PLAN.md

Task — Phase 0 only:
1. Scaffold `api/` as FastAPI + Uvicorn with a venv and requirements.txt.
2. Implement GET /health and POST /analyze that accepts multipart fields: frontal, upper, lower, left, right.
3. /analyze must return DUMMY JSON matching docs/ARCHITECTURE.md (ignore image contents for now). Always include disclaimer.
4. Enable CORS for http://localhost:5173.
5. Add api/.env.example (no real secrets). Ensure .gitignore covers .env, .venv, node_modules, weights/*.pt.
6. Scaffold `web/` as Vite + React + TypeScript + Tailwind.
7. Add VITE_API_URL=http://localhost:8000.
8. Create a simple screen state machine: landing | capture | analyzing | report — empty shells that follow DESIGN.md wireframes (layout only; YOU choose colours/fonts).
9. Add a temporary way to POST to /analyze and console.log or show raw JSON (even a debug button is fine).

Out of scope this phase: real camera, CNN, vision API, Wow, auth.

Done when: from this machine I can run uvicorn on :8000 and npm run dev on :5173; GET /health works; web can successfully POST /analyze and receive dummy JSON.
```

---

## Prompt 1 — Dummy full UI loop

```
Continue the SAME repo on this single computer. Phase 0 scaffold already exists.

Read: docs/DESIGN.md, docs/ARCHITECTURE.md.

Task — Phase 1 only:
Backend:
- Add Pydantic schemas for the analyze response (overall, disclaimer, views, summary with source).
- Improve dummy /analyze so summary includes example crooked, wear, and discoloration findings (so the UI can be built). Same shape as ARCHITECTURE.

Frontend:
- Implement Landing per DESIGN (brand area, 2-minute promise, Start, disclaimer).
- Implement Capture for 5 steps using FILE UPLOAD per view first (camera later): frontal → upper → lower → left → right, with progress "n of 5".
- Implement Analyzing screen while waiting on fetch.
- Implement Report: overall banner, findings list in order crooked → wear → discoloration then other, optional source badges, five thumbnails, disclaimer, Start over.
- Wire FormData POST to http://localhost:8000/analyze with the five field names.

Out of scope: getUserMedia, best.pt, GPT/Gemini, 3D/Wow.

Done when: on this PC, Landing → select 5 images → Analyzing → Report works end-to-end against dummy API with no console errors.
```

---

## Prompt 2 — Real camera capture (+ upload fallback)

```
Continue the SAME repo on this single computer. Phase 1 dummy loop works.

Read: docs/DESIGN.md.

Task — Phase 2 only:
Frontend:
- Add getUserMedia camera preview on Capture (prefer environment facing when available).
- Manual shutter only (user taps Capture) — no auto-capture.
- Retake + Next; store JPEG/PNG blobs in state for all 5 views.
- Simple guide overlay + one-line tip per view.
- Keep gallery/file upload fallback if permission denied.
- Keep posting FormData to localhost:8000/analyze.

Backend:
- Keep dummy analyze.
- Ensure CORS allows http://localhost:5173 (and note how to add LAN origins later for phone).
- Bind reminder: uvicorn --host 0.0.0.0 --port 8000 when we test phone later; for now localhost is enough.

Out of scope: CNN weights, vision API fusion, Wow.

Done when: using this PC's webcam (or phone later), user can capture or upload 5 views and still reach Report via dummy API.
```

---

## Prompt 3 — Load trained CNN (when best.pt exists)

```
Continue the SAME repo on this single computer. Phases 0–2 work with dummy analyze.

Read: docs/ARCHITECTURE.md, docs/DATASETS.md, docs/PLAN.md (CNN owns discoloration).

Preconditions: weights/best.pt and weights/class_map.json exist locally (gitignored OK). If missing, implement loaders so the API still boots and falls back to dummy/CNN-skip without crashing, and document the expected paths.

Task — Phase 3 only:
Backend:
- Load EfficientNet-B0 + best.pt once in FastAPI lifespan.
- Preprocess each uploaded image to 224x224 ImageNet norm; softmax per view.
- Map classes: Tooth Discoloration → discoloration; other Oral Diseases classes → other_visual_concern.
- Confidence threshold default 0.55 (env CONFIDENCE_THRESHOLD).
- /health includes model_loaded and classes.
- /analyze should use CNN results for views/summary discoloration/other when model is loaded; keep overall + disclaimer.

Frontend:
- No redesign — render whatever summary returns with plain language (not raw scary medical dumps as the only label).

Out of scope: GPT/Gemini, fusion with vision, Wow.

Done when: with best.pt present, analyze returns real CNN-based discoloration/other tags; without best.pt, API still runs safely.
```

---

## Prompt 4 — Vision API + fusion (hybrid)

```
Continue the SAME repo on this single computer. CNN path from Phase 3 may or may not have weights.

Read: docs/ARCHITECTURE.md, docs/PLAN.md (hybrid rules), docs/DESIGN.md (report order).

Task — Phase 4 only:
Backend:
- Add vision provider via env: VISION_PROVIDER=openai|gemini and OPENAI_API_KEY or GEMINI_API_KEY in api/.env (never commit secrets).
- Call vision API with all five images; require structured JSON for crooked and wear (optional colour confirm).
- Run CNN (if loaded) and vision in parallel.
- Fuse: discoloration prefer CNN if confident else vision; crooked+wear from vision only; tag source trained_model|vision_api; set overall consider_visit vs no_obvious_concern.
- Degrade gracefully: no vision key → CNN/dummy only with clear health flag; vision fail → don't crash UI.

Frontend:
- Analyzing copy can mention colour, alignment, and wear.
- Show optional source badges.
- Handle slower responses with clear waiting/error retry.

Out of scope: Wow/3D, new screens, training code.

Done when: one POST /analyze returns a single fused report that can include crooked/wear from vision and discoloration from CNN when available; FE displays it on Report.
```

---

## Prompt 5 — Harden for demo on this machine

```
Continue the SAME repo on this single computer. Hybrid analyze from Phase 4 works (or CNN/vision partial with graceful fallback).

Read: docs/DESIGN.md, docs/ARCHITECTURE.md, README.md.

Task — Phase 5 only:
- Solid error UI: camera denied, network fail, analyze 4xx/5xx, missing files.
- Backend: reject oversized images; sensible timeouts; never log raw image bytes.
- Update README with exact commands to run api + web on Windows/Mac, required env vars, where to put best.pt, and disclaimer.
- Add or refresh docs/fixtures/analyze_dummy.json matching current schema.
- Optional: brief comment in README for phone testing (host 0.0.0.0 + CORS for LAN) — don't rebuild architecture.
- Confirm .env and weights are gitignored.

Out of scope: new features, Wow, chatbot.

Done when: a teammate can follow README on this machine and complete a full screening to Report; demo path feels stable.
```

---

## Prompt 6 — Wow slot only (optional; after Phase 1+)

```
Continue the SAME repo. Core Report from Phase 1+ works.

Read: docs/DESIGN.md (Wow slot on Report only), docs/guides/WOW.md.

Task — Phase 6 only:
- Add an optional Wow slot on the Report screen for evidence highlight and/or a simple 2D dental map driven by existing summary JSON.
- Must not change Capture flow or API contract unless absolutely required (prefer no API changes).
- Mobile layout must remain usable.
- YOU choose colours consistent with the existing UI.

Out of scope: chatbot, maps/clinics, auto-capture, redesigning phases 0–5.

Done when: Report still works without Wow; with Wow enabled, findings can show evidence/map without breaking the demo.
```

---

## Operator cheat sheet (you / orchestrator)

| Order | Paste | Wait for |
|---|---|---|
| 1 | Prompt 0 | Both servers + dummy POST |
| 2 | Prompt 1 | Full dummy UI loop |
| 3 | Prompt 2 | Camera/upload captures |
| 4 | Prompt 3 | After `best.pt` ready (or stub fallback) |
| 5 | Prompt 4 | After API key in `api/.env` |
| 6 | Prompt 5 | Demo-stable |
| 7 | Prompt 6 | Only if time |

**Single computer ports:** API `http://localhost:8000` · Web `http://localhost:5173`.

**Trainer:** run separately with `docs/guides/TRAINER.md` — deliver `weights/best.pt` before or during Prompt 3.
