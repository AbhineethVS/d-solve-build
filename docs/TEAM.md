# Team split (4 people — all coding)

**Repo:** https://github.com/AbhineethVS/d-solve-build  

**Rule:** Everyone codes on the core pipeline. Pitch video / story / social post = **endgame only** (last hours before freeze), not a dedicated seat now.

Replace `___` with real names.

| Code | Who | Owns (code) | Do not touch (unless asked) |
|---|---|---|---|
| **P1** | **You** | `api/` — FastAPI app, `/health`, `/analyze`, model load, CORS, dummy→real swap, merges to `main` | Random UI restyles |
| **P2** | ___ | `train/` — dataset prep, `train.py` / Kaggle notebook, metrics plots, export `weights/best.pt` + `class_map.json` | Rewriting API routes |
| **P3** | ___ | `web/` **capture path** — landing → 5 guided steps, camera/`getUserMedia`, upload fallback, blobs in state | Report screen (P4); API internals |
| **P4** | ___ | `web/` **report path** — analyzing screen, report UI, findings list, thumbnails, disclaimer, `api` client (`FormData` POST) | Capture camera logic (P3); training |

**Integration contract (agree once, then code in parallel):**

- Views: `frontal` | `upper` | `lower` | `left` | `right`  
- API: multipart field names = those five; JSON shape in `docs/ARCHITECTURE.md`  
- P3 hands five `Blob`s to shared state; P4 POSTs them and renders the response  
- P2 drops `best.pt` where P1 expects (`weights/`); until then P1 serves **dummy** labels so P3/P4 aren’t blocked  

---

## Why this split

- Four coders, **four folders/surfaces** → fewer merge fights.  
- P1 unifies the pipe; P2 is offline/GPU; P3 and P4 split the UI by screen.  
- Demo/story is everyone’s job **at the end**, not a fourth coding role.

---

## Parallel schedule

### Now → first working loop

| Who | Build |
|---|---|
| **P1** | `api` boots; `/analyze` returns dummy JSON matching the contract |
| **P2** | Kaggle data + training started; paste first val numbers in chat |
| **P3** | 5-step capture works on phone (even if analyze is stubbed) |
| **P4** | Report page renders from **fixture JSON**; then wire real POST |

### When `best.pt` lands

| Who | Build |
|---|---|
| **P1** | Load checkpoint; real predictions |
| **P2** | Confusion matrix + metrics blurb for README |
| **P3** | Capture polish (guides, retake, errors) |
| **P4** | Report polish (banner, confidence, disclaimer) |

### Endgame (all four — not a separate “demo guy”)

- Freeze features  
- Together: README setup, 30s+ pitch video, one backup screen recording, Q&A cheat sheet  
- P1 still owns “does the live demo path work”

---

## Git

- Prefer short branches: `p1/api`, `p2/train`, `p3/capture`, `p4/report`  
- **P1 merges** to `main`  
- Blocked &gt;20 min → ping P1  
- Scope: `docs/PLAN.md` cut list; log changes in `docs/DECISIONS.md`

---

## Q&A speakers (still useful later)

| Topic | Speaker |
|---|---|
| API / architecture | P1 |
| Training / metrics | P2 |
| Capture UX | P3 |
| Report / product flow | P4 |
| Pitch video | whoever is least exhausted — decide Friday evening |
