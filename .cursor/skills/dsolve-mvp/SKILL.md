---
name: dsolve-mvp
description: Locks the DSOLVE 2026 PS1 oral screening MVP scope, stack, cut list, and anti-clone rules. Use when scaffolding, adding features, choosing libraries, or when the user mentions MVP, scope, extras, chatbot, 3D, Flutter, or the dental GitHub capstone.
---

# DSOLVE MVP lock

Read `docs/PLAN.md`, `docs/ARCHITECTURE.md`, `docs/DATASETS.md`, and **`docs/DECISIONS.md`** before expanding scope.

## Decisions log (mandatory)

Any material choice (stack, scope, API shape, model, UX, hosting, reject/accept of a library or feature) → **append a new entry to `docs/DECISIONS.md` in the same turn** (newest at top). Do not edit old entries except to mark `superseded by D-XXX`. Same rule for Cursor, Antigravity, and any other agent.

## Core only (build this first)

1. 5 guided photos → `POST /analyze` → report UI  
2. Train EfficientNet-B0 on Kaggle Oral Diseases → `weights/best.pt`  
3. FastAPI loads checkpoint; Vite React widget captures and displays  

## Stack (do not renegotiate mid-build)

| Layer | Choice |
|---|---|
| Train | PyTorch + timm, Kaggle GPU |
| API | FastAPI + Uvicorn |
| Web | Vite + React + TypeScript + Tailwind |
| Host | Laptop (+ optional tunnel). No cloud required for demo |

## Hard cut until core is green

- Chatbot, clinic locator, auth, DB, Flutter  
- Gemini/VLM as primary classifier  
- 3D jaw pins (phase 2)  
- Wear as a trained class without labeled data  
- Forking [haripatel07/Digital-Dental-Screening…](https://github.com/haripatel07/Digital-Dental-Screening-and-Consultation-System) or any full dental product repo  

Notebooks from public repos: read for concepts only; write original training code.

## Pitch

Lead with: trained model + 5-photo widget + honest screening disclaimer.  
Not: “generic AI that screens teeth.”

## When tempted to add a feature

1. Does core loop work on a real phone? If no → refuse the feature.  
2. If yes → order from `docs/PLAN.md` “After core”.
