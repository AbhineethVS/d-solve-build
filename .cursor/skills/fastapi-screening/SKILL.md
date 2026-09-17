---
name: fastapi-screening
description: FastAPI hybrid oral screening API — multipart uploads, EfficientNet load, vision API (GPT/Gemini) for crooked/wear, fusion into PS1 report JSON. Use when working in api/, /analyze, health, or env keys.
---

# FastAPI — hybrid screening API

## Scope

One service: CNN + vision API + fusion. No auth, no DB, no chatbot.

## Layout

```
api/
  main.py           # app, CORS, lifespan
  schemas.py        # Pydantic report (PS1 concerns + source)
  inference.py      # CNN load + predict
  vision.py         # GPT or Gemini client
  fusion.py         # merge into summary
  requirements.txt
```

## Endpoints

- `GET /health` → `{ status, model_loaded, vision_api_configured, classes }`  
- `POST /analyze` → multipart `frontal|upper|lower|left|right`

Match `docs/ARCHITECTURE.md` (D-015).

## Patterns

- Load `best.pt` once in lifespan; if missing, vision-only still works  
- Env: `MODEL_PATH`, `CORS_ORIGINS`, `CONFIDENCE_THRESHOLD`, `VISION_PROVIDER`, `OPENAI_API_KEY` / `GEMINI_API_KEY`  
- Run CNN + vision **in parallel** (asyncio / threads)  
- Fusion: discoloration prefer CNN; crooked+wear from vision; tag `source`  
- Never commit `.env`; never log raw images  

## Deps

`fastapi`, `uvicorn[standard]`, `python-multipart`, `pillow`, `torch`, `timm`, `pydantic`, `python-dotenv`, `httpx` (or official OpenAI/Google SDKs)

## Local run

```bash
cd api
python -m venv .venv
.\.venv\Scripts\activate
pip install -r requirements.txt
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```
