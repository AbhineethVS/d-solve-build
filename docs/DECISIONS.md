# Decisions log

**Rule for every agent (Cursor, Antigravity, Claude Code, humans):**  
When you choose, change, or reject a stack/scope/API/UX/training approach, **append a new entry here in the same turn** before moving on. Do not rewrite history — supersede with a new entry that references the old ID.

Format (newest at top):

```markdown
## D-NNN — short title
- **Date:** YYYY-MM-DD
- **Status:** accepted | superseded by D-XXX | rejected
- **Decision:** what we chose
- **Why:** 1–3 sentences
- **Rejects / alternatives:** what we did not do
- **Follow-ups:** optional
```

---

## D-018 — Phased FE+BE implementation plan
- **Date:** 2026-09-17
- **Status:** accepted
- **Decision:** Add `docs/IMPLEMENTATION.md` — phases 0–6 for Frontend+Backend together (scaffold → dummy loop → capture → CNN → vision/fusion → harden → optional wow). Trainer parallel from phase 0.
- **Why:** Pair needs a shared build order with exit criteria so they stay in unison.
- **Rejects / alternatives:** Ad-hoc coding without phase gates; waiting on `best.pt` before UI loop.
- **Follow-ups:** Orchestrator enforces exit criteria before advancing.

## D-017 — DESIGN.md layout-only (no prescribed colours)
- **Date:** 2026-09-17
- **Status:** accepted
- **Decision:** Add `docs/DESIGN.md` locking screen layout, flow, hierarchy, and microcopy jobs. Exact colours/fonts left to the implementing agent. Agents building `web/` (and report-facing API) must reference it.
- **Why:** FE/BE/Wow need shared structure without over-constraining visual styling mid-hackathon.
- **Rejects / alternatives:** Full colour-token design system in Markdown; building with no layout contract.
- **Follow-ups:** Cursor rule `design-layout.mdc`; skills point at DESIGN.md.

## D-016 — Team: FE+BE pair, Train, Wow
- **Date:** 2026-09-17
- **Status:** accepted
- **Decision:** Frontend and Backend work in unison on the product loop. Trainer runs GPU training in parallel. Wow builds evidence/visual map on stable report JSON and must not block core. Step-by-step guides in `docs/guides/`.
- **Why:** Pairing FE/BE improves contract quality; dedicated train keeps ML moving; wow is sequenced product polish.
- **Rejects / alternatives:** D-013 four equal code silos (capture/report split); dedicated demo/story seat.
- **Follow-ups:** Fill names in `docs/TEAM.md`.

## D-015 — Hybrid: trained CNN + vision API for PS1 concerns
- **Date:** 2026-09-17
- **Status:** accepted
- **Decision:** Run EfficientNet-B0 (`best.pt`) and a vision API (GPT-4o or Gemini Flash) in parallel on the five photos. CNN owns discoloration (+ other Oral Diseases classes as bonus). Vision API owns crooked teeth and tooth wear. Fuse in FastAPI into one report with `source` per finding.
- **Why:** Oral Diseases labels match discoloration, not crooked/wear. Hybrid satisfies the written PS1 brief without fake wear/crooked training data.
- **Rejects / alternatives:** CNN-only (misses two PS1 concerns); vision-only (weaker “we trained” story); multi-CNN tooth/gum/occlusion stack.
- **Follow-ups:** Pick provider when keys available; update skills; implement fusion in `api/`.

## D-014 — Train on Colab with Kaggle Oral Diseases
- **Date:** 2026-09-17
- **Status:** accepted (host flexible: Colab **or** Kaggle Notebook GPU)
- **Decision:** Dataset = [salmansajid05/oral-diseases](https://www.kaggle.com/datasets/salmansajid05/oral-diseases). Training host = **Google Colab** (GPU). Model remains EfficientNet-B0 → export `best.pt` + metrics for the API.
- **Why:** Team preference; Colab GPU is easy and doesn't require training on the demo laptop. Dataset already chosen for discoloration + related oral classes.
- **Rejects / alternatives:** Training only inside Kaggle Notebooks (still OK as backup); training on CPU laptop.
- **Follow-ups:** Kaggle API token in Colab to download the dataset; keep weights out of git (see `.gitignore`).

## D-013 — All four code; no dedicated demo seat
- **Date:** 2026-09-17
- **Status:** superseded by D-016
- **Decision:** P1=`api/`, P2=`train/`, P3=`web` capture, P4=`web` report+client. Pitch video / story handled together at endgame, not as a standing role.
- **Why:** Team wants everyone coding now; demo work is naturally late-bound.
- **Rejects / alternatives:** D-012 dedicated “demo & story” person.
- **Follow-ups:** Fill names in `docs/TEAM.md`.

## D-012 — Four-person work split (P1 heavy coding)
- **Date:** 2026-09-17
- **Status:** superseded by D-013
- **Decision:** P1 (You) = lead engineer (`api/` + core `web/` + model integration, merges). P2 = ML runner (Kaggle/`best.pt`/metrics). P3 = product UI polish. P4 = demo, pitch video, README/story. Details in `docs/TEAM.md`.
- **Why:** One heavy coder avoids merge chaos; other seats are deliverable-based so no one needs to be a specialist.
- **Rejects / alternatives:** Four people all coding the same features; equal “everyone does everything.”
- **Follow-ups:** Fill real names in `docs/TEAM.md`.

## D-011 — GitHub remote d-solve-build
- **Date:** 2026-09-17
- **Status:** accepted
- **Decision:** Host the project at https://github.com/AbhineethVS/d-solve-build (public empty repo → initial push of docs/skills).
- **Why:** Team needs a shared public GitHub repo per DSOLVE submission rules.
- **Rejects / alternatives:** Private-only local folder with no remote.
- **Follow-ups:** Scaffold `train/`, `api/`, `web/` on this remote after docs land.

## D-010 — Dual skill paths for Cursor + Antigravity
- **Date:** 2026-09-17
- **Status:** accepted
- **Decision:** Keep identical skills under `.agents/skills/` (canonical shared) and `.cursor/skills/` (Cursor).
- **Why:** Antigravity discovers `.agents/skills`, not `.cursor/skills` alone. Same `SKILL.md` open format.
- **Rejects / alternatives:** Skills only in `.cursor/skills` (Cursor-only).
- **Follow-ups:** Edit both trees when changing a skill; optional `.claude/skills` later.

## D-009 — Project agent skills (lean, custom)
- **Date:** 2026-09-17
- **Status:** accepted
- **Decision:** Ship six project skills: `dsolve-mvp`, `ui-ux-screening`, `frontend-design`, `web-capture-widget`, `fastapi-screening`, `pytorch-oral-train`.
- **Why:** Encode MVP lock + stack without vendoring heavy third-party skill CLIs.
- **Rejects / alternatives:** Full `ui-ux-pro-max` CLI install; `fastreact` scaffold (Postgres/JWT/S3).
- **Follow-ups:** See `docs/SKILLS.md`.

## D-008 — Hosting = laptop (+ optional tunnel)
- **Date:** 2026-09-17
- **Status:** accepted
- **Decision:** Demo on local FastAPI + Vite; phone via LAN or Cloudflare/ngrok tunnel. No required cloud deploy for MVP.
- **Why:** Avoid mid-hackathon deploy/cold-start/`best.pt` upload pain. Cloud optional after core works.
- **Rejects / alternatives:** AWS/GCP “proper” infra; GPU endpoint for live pitch.

## D-007 — Do not fork the public dental screening capstone
- **Date:** 2026-09-17
- **Status:** accepted
- **Decision:** Do not use [haripatel07/Digital-Dental-Screening-and-Consultation-System](https://github.com/haripatel07/Digital-Dental-Screening-and-Consultation-System) as a base. Notebooks may be read for training *concepts* only; write original code.
- **Why:** DSOLVE forbids substantial pre-built solutions; repo is public and findable in Q&A.
- **Rejects / alternatives:** Forking Backend/WebApp/Flutter/Chatbot structure or shipping their weights/UI.

## D-006 — Train EfficientNet-B0 on Kaggle Oral Diseases
- **Date:** 2026-09-17
- **Status:** superseded in part by D-014 (training host → Colab; model + dataset unchanged)
- **Decision:** Primary model = ImageNet-pretrained EfficientNet-B0 via `timm`, fine-tuned on Kaggle Oral Diseases; export `weights/best.pt` + `class_map.json`. Train on Kaggle GPU; infer on laptop CPU.
- **Why:** Stronger hackathon story than VLM-only; dataset includes Tooth Discoloration matching PS1; B0 is fast enough for 36h + CPU demo.
- **Rejects / alternatives:** Gemini as primary classifier; ViT/Swin for same demo; inventing a wear class with no labels.
- **Follow-ups:** Optional second model for crooked/crowding only after `best.pt` exists.

## D-005 — Core stack: Vite React + FastAPI + PyTorch
- **Date:** 2026-09-17
- **Status:** accepted
- **Decision:** `web/` = Vite + React + TS + Tailwind; `api/` = FastAPI; `train/` = PyTorch training scripts.
- **Why:** Fastest path to 5-photo widget + trained inference in 36 hours.
- **Rejects / alternatives:** Flutter; Next.js ceremony; chatbot; clinic map; auth/DB in core.

## D-004 — Extras parked until core pipeline works
- **Date:** 2026-09-17
- **Status:** accepted
- **Decision:** No 3D pins, chatbot, maps, dual apps, or polish features until: 5 photos → `/analyze` → report works end-to-end (dummy model OK until checkpoint ready).
- **Why:** Capstone-shaped extras kill the demo; PS1 only requires guided capture + visual report.
- **Rejects / alternatives:** Building differentiators before the pipe.

## D-003 — Five guided views
- **Date:** 2026-09-17
- **Status:** accepted
- **Decision:** Capture order/fields: `frontal`, `upper`, `lower`, `left`, `right`.
- **Why:** Matches PS1 “five quick images” and supports a simple multipart API contract.
- **Rejects / alternatives:** Freeform single upload only; X-ray path in core.

## D-002 — Problem statement = PS1
- **Date:** 2026-09-17
- **Status:** accepted
- **Decision:** Build DSOLVE 2026 Problem 1 — Oral Health Screening Widget.
- **Why:** Team choice; fits trained CV + mobile web widget story.
- **Rejects / alternatives:** PS2–PS6 for now (pivot allowed by rules if blocked).

## D-001 — Decisions log required
- **Date:** 2026-09-17
- **Status:** accepted
- **Decision:** Maintain this file; all agents must append when making material decisions.
- **Why:** Multi-tool team (Cursor / Antigravity / others) needs one source of truth mid-hackathon.
- **Rejects / alternatives:** Decisions only in chat history.
