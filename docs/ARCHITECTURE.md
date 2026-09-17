# Architecture — hybrid core pipeline

```
[ Web: 5 guided captures ]
            |
            | multipart/form-data
            v
[ FastAPI  POST /analyze ]
            |
      ┌─────┴─────┐
      │ parallel  │
      v           v
[ EfficientNet-B0 ]   [ Vision API: GPT-4o or Gemini Flash ]
[ best.pt Softmax ]   [ crooked + wear (+ optional colour) ]
      │           │
      └─────┬─────┘
            v
[ Fusion → PS1 report JSON ]
            |
            v
[ Web: photos + findings + overall ]
```

Training is offline (**Kaggle Notebook or Colab** GPU + Kaggle Oral Diseases). Laptop runs CNN inference + vision API calls.

---

## Folders

| Path | Responsibility |
|---|---|
| `train/` | Train scripts / notebook notes, class map, plots |
| `api/` | `/analyze`, CNN loader, vision client, fusion |
| `web/` | Capture + report |
| `weights/` | `best.pt` + `class_map.json` (not huge commits) |
| `docs/` | Plan, architecture, decisions |

---

## API contract (MVP)

### `GET /health`

```json
{
  "status": "ok",
  "model_loaded": true,
  "vision_api_configured": true,
  "classes": ["..."]
}
```

### `POST /analyze`

**Request:** multipart — `frontal`, `upper`, `lower`, `left`, `right` (files).

**Response:**

```json
{
  "overall": "consider_visit",
  "disclaimer": "Screening aid only. Not a diagnosis.",
  "views": [
    {
      "view": "frontal",
      "label": "Tooth Discoloration",
      "confidence": 0.81,
      "report_tag": "discoloration",
      "source": "trained_model"
    }
  ],
  "summary": [
    {
      "concern": "discoloration",
      "views": ["frontal", "upper"],
      "max_confidence": 0.81,
      "source": "trained_model",
      "note": "Possible staining on visible surfaces"
    },
    {
      "concern": "crooked",
      "views": ["frontal"],
      "max_confidence": 0.72,
      "source": "vision_api",
      "note": "Crowding suggested on anterior teeth"
    },
    {
      "concern": "wear",
      "views": ["frontal", "upper"],
      "max_confidence": 0.64,
      "source": "vision_api",
      "note": "Possible incisal wear"
    }
  ]
}
```

`concern` values for PS1: `discoloration` | `crooked` | `wear` | `other_visual_concern`  
`source`: `trained_model` | `vision_api`  
`overall`: `consider_visit` | `no_obvious_concern`

---

## Fusion rules

1. Run CNN on each view (224×224). Threshold default **0.55**.  
2. Call vision API once with all five images + strict JSON schema for crooked / wear / discoloration.  
3. Map CNN classes: Tooth Discoloration → `discoloration`; other disease classes → `other_visual_concern`.  
4. Prefer CNN for `discoloration` when above threshold; otherwise use vision API colour signal.  
5. Always take crooked + wear from vision API.  
6. If CNN weights missing → vision-only (still demoable).  
7. If vision API fails → CNN-only + report notes that crooked/wear unavailable.

---

## Frontend screens (core)

1. Landing → 2. Capture (×5) → 3. Analyzing → 4. Report (PS1 concerns + source + disclaimer)

---

## Config / secrets

| Item | Where |
|---|---|
| `MODEL_PATH` | path to `best.pt` |
| `CORS_ORIGINS` | web origin(s) |
| `CONFIDENCE_THRESHOLD` | default `0.55` |
| `VISION_PROVIDER` | `openai` or `gemini` |
| `OPENAI_API_KEY` / `GEMINI_API_KEY` | `.env` only — never commit |

---

## Non-goals for architecture v1

- Auth, DB, chatbot, Flutter  
- Learned fusion network  
- Training wear/crooked CNNs  
