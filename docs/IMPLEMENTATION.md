# Implementation plan — one computer, Codex + your manual tests

Frontend and backend work **together on a single PC**, in one repo. You drive Codex **one phase at a time**: paste the prompt, let it finish, then **you** run the manual tests in that phase. Do not start the next phase until those tests pass.

**Read before Phase 0:** `docs/DESIGN.md`, `docs/ARCHITECTURE.md`, `docs/PLAN.md`

**Ports on this machine:** API `http://localhost:8000` · Web `http://localhost:5173`

**Trainer** is separate (`docs/guides/TRAINER.md`). They deliver `weights/best.pt` before or during Phase 3. Do not block Phases 0–2 on training.

---

# Phase 0 — Scaffold the product shell

## What this phase is supposed to accomplish

Phase 0 does not deliver a beautiful screening experience and it does not run any real AI. It exists to prove that **both halves of the product can live on one machine and talk to each other**.

By the end of Phase 0 you should have:

- An `api/` FastAPI app that boots, answers health checks, and accepts the five named image fields on `/analyze`.
- A `/analyze` handler that **ignores what is in the images** and returns a **valid dummy report** in the exact JSON shape from `docs/ARCHITECTURE.md` (so the UI can be built against a stable contract).
- A `web/` Vite + React + TypeScript + Tailwind app with a screen state machine for Landing → Capture → Analyzing → Report (even if Capture is empty or crude).
- CORS and env wiring so the browser on `localhost:5173` can call `localhost:8000` without fighting the browser.
- Enough of a “call the API” path that you can prove the POST works (a temporary debug control is acceptable).

If Phase 0 is wrong, every later phase becomes merge pain. If Phase 0 is right, Frontend and Backend are already “together” on one laptop.

## Codex prompt — paste this entire block

```
You are implementing our DSOLVE oral health screening widget. Frontend and backend run on ONE computer in ONE repo. Work only on Phase 0.

Before writing code, read:
- docs/DESIGN.md (layout and screens only; YOU choose colours and fonts — do not invent extra screens)
- docs/ARCHITECTURE.md (API contract)
- docs/PLAN.md (hybrid product intent; do not implement CNN or vision yet)

Create and wire:

1) api/
- Python FastAPI + Uvicorn, venv, requirements.txt
- GET /health returning JSON that at least includes status
- POST /analyze accepting multipart form fields named exactly: frontal, upper, lower, left, right
- For Phase 0, do not run ML. Return a DUMMY JSON body that matches docs/ARCHITECTURE.md: overall, disclaimer, views (optional), summary array. Always include a screening-not-diagnosis disclaimer string.
- Enable CORS for http://localhost:5173
- Add api/.env.example with placeholders only (no real secrets)
- Ensure repo .gitignore covers .env, .venv, node_modules, dist, weights/*.pt and similar

2) web/
- Vite + React + TypeScript + Tailwind in web/
- VITE_API_URL defaulting to http://localhost:8000
- App state machine with screens: landing | capture | analyzing | report
- Empty or minimal shells for those screens following DESIGN.md structure (brand/start on landing; placeholder capture; analyzing; report placeholder)
- A reliable way to POST the five fields to /analyze and show that the dummy JSON came back (temporary debug button on landing or capture is fine)

Do not implement: real camera pipeline, EfficientNet, GPT/Gemini, Wow/3D, auth, database, chatbot.

When finished, print exact commands to run the API and the web app on this machine (Windows-friendly if the OS is Windows).
```

## Manual tests you must run (Phase 0)

Do these yourself after Codex stops. Use this machine only.

**Test A — API process**  
Create/activate the venv if needed, install requirements, start Uvicorn on port 8000 (host 127.0.0.1 or 0.0.0.0). In a browser or with curl, open `http://localhost:8000/health`. You should see JSON with a healthy status, not a connection error.

**Test B — Dummy analyze without the UI**  
Send a multipart POST to `http://localhost:8000/analyze` with five files (any small JPGs/PNGs are fine) under field names `frontal`, `upper`, `lower`, `left`, `right`. Confirm the response JSON includes `overall`, `disclaimer`, and a `summary` array (or equivalent fields exactly as ARCHITECTURE describes). Missing fields or a 422 means the contract is not ready — fix before Phase 1.

**Test C — Web process**  
From `web/`, install deps and run the Vite dev server. Open `http://localhost:5173`. The app should load without a blank error screen. You should be able to recognize the Landing shell (brand + start area) even if styling is rough.

**Test D — Browser → API on the same PC**  
Using whatever debug control Codex added, trigger a POST from the web app to the API. In the Network tab, the request should go to `localhost:8000`, status 200, and the response body should be the dummy report. CORS failures (blocked by browser) mean Phase 0 is not done.

**Test E — Screen state exists**  
Click through or temporarily force navigation so you can see that Capture, Analyzing, and Report routes/states exist as shells. They can be ugly; they must not be missing.

## Phase 0 pass / fail

**Pass:** Health works, multipart analyze returns contract-shaped dummy JSON, Vite app loads, browser POST succeeds with CORS OK, four screen states exist.  
**Fail:** Only one of api/web exists; analyze shape is invented differently from ARCHITECTURE; web cannot call API; secrets committed.

Only after **Pass**, move to Phase 1.

---

# Phase 1 — Dummy end-to-end screening loop

## What this phase is supposed to accomplish

Phase 1 makes the **product story clickable** without real AI. A user (you) can start on Landing, provide five images (uploads are enough), wait on Analyzing, and read a Report that looks like the real thing: overall recommendation, findings for crooked / wear / discoloration, thumbnails, disclaimer, start over.

The backend still returns **dummy** data, but the dummy must be rich enough that the Report UI is not empty. This is the rehearsal of the final flow.

## Codex prompt — paste this entire block

```
Continue the SAME repo on ONE computer. Phase 0 is done (api + web scaffold, dummy /analyze, CORS).

Read docs/DESIGN.md and docs/ARCHITECTURE.md again.

Phase 1 only — build the full dummy screening loop:

Backend:
- Add clear Pydantic models for the analyze response.
- Upgrade dummy /analyze so `summary` includes plausible example findings for crooked, wear, and discoloration (and optional source fields). Keep overall + disclaimer. Still do not run ML.

Frontend:
- Landing per DESIGN: promise (~2 minutes / 5 photos), Start, disclaimer.
- Capture: five steps in order frontal → upper → lower → left → right. For Phase 1, file upload per step is enough. Show progress like "2 of 5" and the view name. Store the five files/blobs for submit.
- Analyzing: shown while POST /analyze is in flight.
- Report: overall banner (consider_visit vs no_obvious_concern), findings list preferring crooked → wear → discoloration then other, optional source badges, five thumbnails, disclaimer, Start over.
- Submit with FormData field names exactly matching the API.

Do not add getUserMedia yet. Do not add CNN or vision API. Do not add Wow.

Done means: a human can complete Landing → five uploads → Report against the dummy API with no console errors.
```

## Manual tests you must run (Phase 1)

**Test A — Happy path**  
Start the API and web. From Landing hit Start. On each Capture step upload any image. After the fifth, confirm Analyzing appears, then Report. Overall banner and at least one finding should be visible. Thumbnails should reflect what you uploaded. Start over returns to Landing or first capture cleanly.

**Test B — Finding order / content**  
On Report, confirm crooked, wear, and discoloration-style findings can appear (from dummy data). Plain language should dominate; the screen should not look like a raw model dump.

**Test C — Failure visibility**  
Stop the API and submit again. The UI should show a recoverable error (not a silent hang). Start the API and retry successfully.

**Test D — Field names**  
In Network tab, confirm the POST body uses `frontal`, `upper`, `lower`, `left`, `right`.

## Phase 1 pass / fail

**Pass:** Full loop works on this PC with uploads; Report is understandable; errors are visible if API is down.  
**Fail:** Skips Analyzing; Report blank; wrong field names; must use camera to proceed.

---

# Phase 2 — Real capture on this machine

## What this phase is supposed to accomplish

Phase 2 replaces “upload only” with a **real guided capture** experience: camera preview, manual shutter, retake, tips, progress, and upload fallback if the camera is denied. The API can stay on dummy responses. Goal: the five images feel like the PS1 widget, not a file form.

## Codex prompt — paste this entire block

```
Continue the SAME repo on ONE computer. Phase 1 dummy loop works with uploads.

Read docs/DESIGN.md (Capture wireframe).

Phase 2 only:
- Add getUserMedia preview on Capture. Prefer environment facing when available; laptop webcam is fine for development on this PC.
- Manual capture only (user taps Capture). No auto-shutter.
- Retake and Next; keep blobs for all five views; progress and per-view tips; simple guide overlay.
- Keep file/gallery upload fallback if permission is denied.
- Still POST FormData to http://localhost:8000/analyze (dummy OK).
- Backend: keep dummy analyze; ensure CORS for http://localhost:5173; document that phone testing later may need uvicorn --host 0.0.0.0 and extra CORS origins — do not over-build.

Out of scope: CNN, vision fusion, Wow, new screens.
```

## Manual tests you must run (Phase 2)

**Test A — Camera path**  
Allow camera permission. Confirm preview. Capture a frame, retake, capture again, advance through all five views, reach Report.

**Test B — Deny camera**  
Block permission (or use a browser profile without camera). Confirm upload fallback still completes the loop.

**Test C — Blob integrity**  
Thumbnails on Report should match what you captured, not placeholders from Phase 1 demos.

## Phase 2 pass / fail

**Pass:** Camera or fallback produces five real images and a Report on this PC.  
**Fail:** Auto-capture required; no fallback; preview broken on Chrome/Edge you will demo with.

---

# Phase 3 — Connect the trained CNN

## What this phase is supposed to accomplish

Phase 3 teaches `/analyze` to use **EfficientNet-B0 + `best.pt`** for discoloration (and other Oral Diseases classes as `other_visual_concern`). Crooked and wear are still not the CNN’s job. If weights are missing, the API must still boot (fallback), because the pair should not be bricked.

## Codex prompt — paste this entire block

```
Continue the SAME repo on ONE computer. Phases 0–2 work.

Read docs/ARCHITECTURE.md, docs/DATASETS.md, docs/PLAN.md (CNN owns discoloration).

Phase 3 only — CNN path:
- Load EfficientNet-B0 with weights/best.pt and weights/class_map.json in FastAPI lifespan when present.
- Preprocess uploads to 224x224 with ImageNet normalization; softmax per view.
- Map Tooth Discoloration → discoloration; other dataset classes → other_visual_concern; threshold via env CONFIDENCE_THRESHOLD default 0.55.
- /health must report model_loaded and classes.
- If weights are missing, do not crash: skip CNN and keep a safe dummy or empty-CNN behavior so the UI still works.
- Frontend: keep layout; show plain-language findings from summary.

Out of scope: GPT/Gemini vision calls, fusion with crooked/wear, Wow.
```

## Manual tests you must run (Phase 3)

**Test A — Without weights**  
Temporarily rename `best.pt` if present. API still starts. `/health` shows model not loaded. UI loop still completes.

**Test B — With weights**  
Restore `best.pt` + `class_map.json`. Restart API. `/health` shows model_loaded true. Run analyze with real mouth photos or dataset samples. Confirm discoloration/other tags can appear from the model (values need not be perfect).

**Test C — Stability**  
Two or three consecutive analyzes without crashing the worker.

## Phase 3 pass / fail

**Pass:** Safe without weights; with weights, CNN-derived tags appear in the report path.  
**Fail:** API won’t start without weights; UI broken when model loads.

---

# Phase 4 — Vision API + fusion (hybrid)

## What this phase is supposed to accomplish

Phase 4 completes the **PS1 brief**: vision API covers **crooked** and **wear**; CNN covers **discoloration** when available; FastAPI **fuses** one report with `source` labels. This is the intelligence phase. Latency will rise; the UI must wait honestly.

## Codex prompt — paste this entire block

```
Continue the SAME repo on ONE computer. Phase 3 CNN path exists (weights optional at runtime).

Read docs/ARCHITECTURE.md, docs/PLAN.md hybrid rules, docs/DESIGN.md report order.

Phase 4 only — vision + fusion:
- Env: VISION_PROVIDER=openai|gemini and OPENAI_API_KEY or GEMINI_API_KEY in api/.env (never commit secrets).
- Send all five images to the vision API with a strict schema for crooked and wear (optional colour confirm). Screening language only.
- Run CNN (if loaded) and vision in parallel; fuse: discoloration prefers CNN when confident else vision; crooked+wear from vision; tag source; set overall; always disclaimer.
- Graceful degrade if key missing or vision fails.
- Frontend: analyzing copy may mention colour/alignment/wear; optional source badges; clear waiting and retry on failure.

Out of scope: Wow/3D, auth, chatbot, retraining.
```

## Manual tests you must run (Phase 4)

**Test A — No API key**  
Without key, app still runs; health shows vision not configured; Report still possible via CNN/dummy; no crash.

**Test B — With API key**  
Put key in `api/.env`, restart API. Run a full capture. Report should be able to show crooked and/or wear from vision when relevant. Discoloration should still be able to come from CNN if loaded. Check `source` values if shown.

**Test C — Latency / errors**  
Confirm Analyzing remains visible during the slower call. Disconnect network mid-request once: UI should error recoverably.

## Phase 4 pass / fail

**Pass:** One fused report; crooked/wear path works with key; degrade without key.  
**Fail:** FE merges two APIs itself; secrets committed; hard crash without key.

---

# Phase 5 — Demo hardening on this machine

## What this phase is supposed to accomplish

Phase 5 makes the demo **repeatable**. Someone else on the team (or you under stress) can follow the README, run both processes, and finish a screening. Errors are understandable. No secrets in git.

## Codex prompt — paste this entire block

```
Continue the SAME repo on ONE computer. Hybrid path from Phase 4 works or degrades gracefully.

Read docs/DESIGN.md, docs/ARCHITECTURE.md, README.md.

Phase 5 only — harden for demo:
- Camera/network/analyze error states with clear recovery.
- Backend limits on oversized images; sensible timeouts; never log raw image bytes.
- README: exact commands for api + web on this OS, env vars, where to place best.pt, disclaimer, optional phone note (0.0.0.0 + CORS).
- Add or refresh docs/fixtures/analyze_dummy.json to match the live schema.
- Verify .gitignore for .env and weights.

No new product features. No Wow unless already present.
```

## Manual tests you must run (Phase 5)

**Test A — Cold start from README**  
Stop everything. Follow README only. Both servers start; one full screening succeeds.

**Test B — Git hygiene**  
`git status` shows no `.env` and no accidental huge weight commit.

**Test C — Demo dry run**  
Time yourself through a full flow as if mentors are watching. Note any stuck step and fix before calling the phase done.

## Phase 5 pass / fail

**Pass:** README-only cold start works; errors are human; secrets safe.  
**Fail:** Only you know tribal run steps; demo randomly hangs.

---

# Phase 6 — Wow on Report (optional)

## What this phase is supposed to accomplish

Phase 6 adds **evidence highlight and/or a simple visual map** on Report only. It must not destabilize Capture or the API contract. Skip entirely if Phase 5 is late.

## Codex prompt — paste this entire block

```
Continue the SAME repo. Core Report works (Phase 1+).

Read docs/DESIGN.md (Wow slot) and docs/guides/WOW.md.

Phase 6 only:
- Add an optional Wow area on Report for evidence focus and/or simple 2D dental map using existing summary + thumbnails.
- Prefer zero API changes.
- Keep mobile usable; match existing UI language (you choose colours).
- Capture flow and phases 0–5 behavior must remain intact.

No chatbot, clinic map, auto-capture, or new core screens.
```

## Manual tests you must run (Phase 6)

**Test A — Core still works**  
Full screening without using Wow controls still reaches a correct Report.

**Test B — Wow interaction**  
If findings exist, interacting with Wow (tap finding / pin) shows evidence or map context without breaking Start over.

**Test C — Narrow width**  
Resize to ~375px wide; nothing critical overflows off-screen.

## Phase 6 pass / fail

**Pass:** Wow is additive; core demo safe.  
**Fail:** Capture regressed; API rewritten for cosmetics.

---

# How you run this plan day-of

1. Paste **Phase 0** prompt into Codex → run **Phase 0 manual tests** → only then continue.  
2. Same for Phases 1 → 2 → 3 → 4 → 5 → (6 if time).  
3. Keep FE and BE on this one machine (`localhost`) until Phase 5 phone notes.  
4. When a test fails, stay in that phase; do not “prompt ahead.”
