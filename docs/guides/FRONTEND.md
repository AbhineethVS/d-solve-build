# Frontend guide (FE)

You own **`web/`**. You work **in unison with Backend** — same contract, frequent sync. Do not invent API fields; change them with BE and update `docs/ARCHITECTURE.md`.

Related: follow **`docs/DESIGN.md`** for layout wireframes (colours are free). Skill: `ui-ux-screening`.

---

## Your job in one line

Ship a mobile-first widget: **Landing → 5 captures → Analyzing → Report** that calls `POST /analyze` and shows PS1 findings (crooked / wear / discoloration).

---

## Before you write UI

1. Read `docs/PLAN.md` (hybrid) and `docs/ARCHITECTURE.md` (JSON shape).  
2. Agree with BE on: base URL, field names (`frontal|upper|lower|left|right`), response `summary[]`.  
3. Get a **fixture JSON** from BE (or copy the example in ARCHITECTURE) so you can build report before the real model exists.  
4. Sync with BE every ~1 hour: “does the contract still match?”

---

## Step-by-step build order

### Step F1 — Scaffold (30–60 min)

- [ ] `npm create vite@latest` (React + TS) in `web/` if missing  
- [ ] Add Tailwind  
- [ ] Env: `VITE_API_URL=http://localhost:8000`  
- [ ] App shell with 4 screens controlled by simple state (no heavy router required)

**Done when:** blank app runs on phone/laptop browser.

### Step F2 — Landing (30 min)

- [ ] Product name, “2-minute screening”, Start CTA  
- [ ] Disclaimer: not a diagnosis  
- [ ] Follow `ui-ux-screening` tokens (teal clinical, not purple AI sludge)

**Done when:** Start goes to capture step 1.

### Step F3 — Capture flow (2–4 hrs) — **priority**

Views in order: `frontal` → `upper` → `lower` → `left` → `right`.

For each step:

- [ ] Title + short tip (“smile naturally”, “upper teeth”, …)  
- [ ] Camera via `getUserMedia` Prefer `facingMode: 'environment'`  
- [ ] Capture → JPEG/PNG blob in state  
- [ ] Retake + Next  
- [ ] Progress: `2 of 5`  
- [ ] Fallback: file upload / gallery if camera fails  

**Done when:** five blobs exist in memory and you can preview thumbnails.

### Step F4 — Analyzing screen (20 min)

- [ ] Spinner / calm copy while `fetch` runs  
- [ ] Disable back during request (or allow cancel cleanly)

### Step F5 — API client (1 hr) — **with BE**

- [ ] `FormData` append five files with exact field names  
- [ ] `POST ${VITE_API_URL}/analyze`  
- [ ] Handle network errors with plain language  
- [ ] First wire against BE **dummy** response  

**Done when:** dummy JSON reaches the report screen.

### Step F6 — Report screen (2–3 hrs)

- [ ] Overall banner: `consider_visit` / `no_obvious_concern`  
- [ ] List `summary` concerns — lead with crooked, wear, discoloration  
- [ ] Show confidence + short `note`  
- [ ] Optional badge: `source` (`trained_model` / `vision_api`)  
- [ ] Thumbnail strip of the five photos  
- [ ] Persistent disclaimer  
- [ ] Restart screening  

**Done when:** fixture + dummy + (later) real API all render.

### Step F7 — Phone demo polish (1–2 hrs)

- [ ] Test on real phone (same Wi‑Fi or tunnel URL from BE)  
- [ ] 375px layout, big tap targets  
- [ ] Empty / permission-denied states  

---

## When (timeline)

| Window | You should be on |
|---|---|
| Tonight | F1–F5 against dummy API |
| Fri morning | F6 solid; phone capture works |
| Fri afternoon | Real API; polish |
| Endgame | Help Wow integrate; pitch recording |

---

## Sync rules with Backend

- Contract changes → update ARCHITECTURE together, then both code  
- If BE is down → keep developing on fixture JSON  
- Never commit API keys  
- Ask BE for CORS if browser blocks  

---

## Out of scope for you

Chatbot, maps, auth, Flutter, redesigning fusion logic, training models.
