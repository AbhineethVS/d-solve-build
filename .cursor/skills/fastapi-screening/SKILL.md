---
name: fastapi-screening
description: FastAPI patterns for the oral screening analyze API — multipart uploads, model load on startup, Pydantic report schema, CORS, dummy-to-real checkpoint swap. Use when working in api/, writing /analyze, health checks, or serving the React build.
---

# FastAPI — screening API

## Scope

One service. No auth, no DB, no chatbot routes in core.

## Layout

```
api/
  main.py          # app, CORS, lifespan
  schemas.py       # Pydantic request/response
  inference.py     # load model, predict_one, aggregate
  requirements.txt
```

## Endpoints

- `GET /health` → `{ status, model_loaded, classes }`  
- `POST /analyze` → multipart fields: `frontal`, `upper`, `lower`, `left`, `right`

Match response shape in `docs/ARCHITECTURE.md`.

## Patterns

- Load `best.pt` once in **lifespan** (not per request)  
- Env: `MODEL_PATH`, `CORS_ORIGINS`, `CONFIDENCE_THRESHOLD` (default `0.55`)  
- If weights missing: still boot; `/analyze` returns clearly marked **dummy** predictions so UI can develop  
- CPU inference; `torch.inference_mode()`  
- Validate content-type loosely (jpeg/png/webp); reject huge files (e.g. >8MB each)  
- Never log raw image bytes  

## Aggregation

1. Softmax per view  
2. Map class → `report_tag` via `class_map.json`  
3. Any concern above threshold → `overall: consider_visit` else `no_obvious_concern`  
4. Always include disclaimer string  

## Deps (keep lean)

`fastapi`, `uvicorn[standard]`, `python-multipart`, `pillow`, `torch`, `timm`, `pydantic`, `python-dotenv`

No SQLAlchemy, no JWT, no Redis for MVP.

## Local run

```bash
cd api
python -m venv .venv
.\.venv\Scripts\activate
pip install -r requirements.txt
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```
