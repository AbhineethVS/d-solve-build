# Architecture — core pipeline

```
[ Web: 5 guided captures ]
            |
            | multipart/form-data
            v
[ FastAPI  POST /analyze ]
            |
            | preprocess 224x224
            v
[ EfficientNet-B0 + best.pt ]
            |
            | softmax per image
            v
[ Aggregate → screening report JSON ]
            |
            v
[ Web: photos + findings + overall ]
```

Training is offline (Kaggle GPU). The venue laptop only runs inference.

---

## Folders

| Path | Responsibility |
|---|---|
| `train/` | Download notes, `train.py`, transforms, class map, plots |
| `api/` | App, model loader, `/analyze`, schemas |
| `web/` | Capture screens, API client, report screen |
| `weights/` | `best.pt` (+ `class_map.json`) — prefer LFS or external zip |
| `docs/` | This documentation |

---

## API contract (MVP)

### `GET /health`

```json
{ "status": "ok", "model_loaded": true, "classes": ["..."] }
```

### `POST /analyze`

**Request:** multipart form

| Field | Type | Required |
|---|---|---|
| `frontal` | file | yes |
| `upper` | file | yes |
| `lower` | file | yes |
| `left` | file | yes |
| `right` | file | yes |

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
      "report_tag": "discoloration"
    }
  ],
  "summary": [
    {
      "concern": "discoloration",
      "views": ["frontal", "upper"],
      "max_confidence": 0.81
    }
  ]
}
```

`overall` values:

- `consider_visit`
- `no_obvious_concern`

`report_tag` mapping lives in `api` (single source of truth with `class_map.json`).

---

## Inference rules (simple, demo-safe)

1. Run classifier independently on each of the 5 images.  
2. Confidence threshold (start at **0.55**, tune after val metrics).  
3. If any view above threshold maps to a concern → `consider_visit`.  
4. Return raw label + mapped tag so the UI can stay dumb.

No tooth-level FDI in core. Views imply which region was photographed.

---

## Frontend screens (core)

1. **Landing** — name, 2-minute promise, disclaimer, Start  
2. **Capture** — one screen per view with silhouette / tip text; retake allowed  
3. **Analyzing** — spinner while `POST /analyze`  
4. **Report** — overall banner, list of findings, thumbnails of the 5 shots  

Mobile-first CSS. Demo on phone via same Wi-Fi or tunnel.

---

## Config / secrets

| Item | Where |
|---|---|
| Model path | `api` env `MODEL_PATH` (default `../weights/best.pt`) |
| CORS origins | `api` env `CORS_ORIGINS` |
| No API keys required for core trained path | — |

Do not commit `.env` with secrets. Dataset stays out of git (use Kaggle download in `train`).

---

## Non-goals for architecture v1

- Auth, DB, queues, Docker-for-demo-only  
- Streaming video, on-device TFLite (optional later)  
- Calling external VLMs as the primary classifier  
