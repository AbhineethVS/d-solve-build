# Team split (locked) — pair + train + wow

**Repo:** https://github.com/AbhineethVS/d-solve-build  

| Seat | Who | Owns |
|---|---|---|
| **FE** | ___ | `web/` — capture + report UI (works **in unison** with BE) |
| **BE** | ___ | `api/` — `/analyze`, CNN load, vision API, **fusion** (works **in unison** with FE) |
| **Train** | ___ | Kaggle/Colab → `best.pt` + metrics (parallel) |
| **Wow** | ___ | Evidence / visual map on top of stable report JSON (does not block core) |

**Rule:** FE + BE sit together (or same chat), share one API contract, sync ~hourly. Train and Wow stay parallel but **do not** redesign the API without the pair.

Pitch video / social = **endgame**, everyone’s job for an hour — not a fifth seat.

---

## Guides (step-by-step)

| Role | Guide |
|---|---|
| Frontend | [`docs/guides/FRONTEND.md`](guides/FRONTEND.md) |
| Backend | [`docs/guides/BACKEND.md`](guides/BACKEND.md) |
| Trainer | [`docs/guides/TRAINER.md`](guides/TRAINER.md) |
| Wow | [`docs/guides/WOW.md`](guides/WOW.md) |

Contract law: [`docs/ARCHITECTURE.md`](ARCHITECTURE.md) · Plan: [`docs/PLAN.md`](PLAN.md) · **Phases:** [`docs/IMPLEMENTATION.md`](IMPLEMENTATION.md)

---

## How the four move (timeline)

```
Now → first loop:     FE+BE lock JSON → dummy /analyze → 5 photos → report
Parallel:             Train starts GPU job
When JSON stable:     Wow builds evidence/map against fixture JSON
When best.pt ready:   BE loads CNN (FE unchanged)
When API key ready:   BE adds vision path (crooked/wear)
Endgame:              polish + pitch together
```

---

## Git

- Branches: `fe/...`, `be/...`, `train/...`, `wow/...`  
- Prefer **BE or FE lead merges** to `main` after a quick look  
- Blocked >20 min → ping the pair  
- Log material changes in `docs/DECISIONS.md`
