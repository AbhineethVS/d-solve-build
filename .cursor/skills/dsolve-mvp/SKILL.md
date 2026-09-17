---
name: dsolve-mvp
description: Locks the DSOLVE 2026 PS1 oral screening MVP — hybrid CNN + vision API, scope, cut list, anti-clone rules. Use when scaffolding, adding features, or discussing MVP/extras.
---

# DSOLVE MVP lock

Read `docs/PLAN.md`, `docs/ARCHITECTURE.md`, `docs/DATASETS.md`, and **`docs/DECISIONS.md`** before expanding scope.

## Decisions log (mandatory)

Any material choice → **append to `docs/DECISIONS.md`** in the same turn (newest at top). Same for Cursor, Antigravity, and other agents.

## Core only (build this first)

1. 5 guided photos → `POST /analyze` → report UI  
2. Train EfficientNet-B0 on Oral Diseases → `best.pt` (discoloration + other)  
3. Vision API (GPT/Gemini) for **crooked + wear**  
4. Fuse in FastAPI; React shows PS1 concerns with `source`  

## Stack (do not renegotiate mid-build)

| Layer | Choice |
|---|---|
| Train | PyTorch + timm, Kaggle or Colab GPU |
| Vision | GPT-4o or Gemini Flash via API |
| API | FastAPI (CNN + vision + fusion) |
| Web | Vite + React + TypeScript + Tailwind |
| Host | Laptop (+ optional tunnel) |

## Hard cut until core is green

- Chatbot, clinic locator, auth, DB, Flutter  
- 3D jaw pins (phase 2)  
- Training wear/crooked CNNs without labels  
- Forking public dental screening product repos  

## Pitch

Lead with hybrid: trained colour model + vision API for alignment/wear. Screening ≠ diagnosis.
