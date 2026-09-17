---
name: web-capture-widget
description: Build the Vite React TypeScript Tailwind 5-photo oral screening frontend — camera capture, guided steps, analyze client, report screen. Use when working in web/, getUserMedia, upload fallback, or wiring POST /analyze.
---

# Web capture widget

## Stack

Vite + React + TypeScript + Tailwind. Mobile-first. No Flutter.

## Flow state

```
landing → capture[viewIndex 0..4] → analyzing → report | error
```

Views in order: `frontal`, `upper`, `lower`, `left`, `right`.

## Camera

- Prefer `getUserMedia({ video: { facingMode: 'environment' } })`  
- Mirror off for rear camera dental shots (don’t selfie-mirror clinical photos)  
- Capture to canvas → JPEG/PNG `Blob`  
- Keep blobs in memory; revoke object URLs on unmount  
- Fallback: `<input type="file" accept="image/*" capture="environment">`  

## API client

`POST /analyze` as `FormData` with the five field names matching the API.  
Base URL from `import.meta.env.VITE_API_URL` (default `http://localhost:8000`).

## Components (keep flat)

- `Landing`  
- `CaptureStep`  
- `Analyzing`  
- `Report`  
- `Disclaimer`  

No router complexity required — local state is enough for MVP.

## Report rendering

- Overall status banner  
- List of findings (`concern`, confidence %, source views)  
- Thumbnail strip of the five images  
- Always show disclaimer  

## Phone demo

- Vite + API on `0.0.0.0`  
- Same Wi-Fi or tunnel  
- Test camera permissions on HTTPS/tunnel if browser blocks insecure origins  

## Do not add in core

Auth screens, chatbot drawer, maps, shadcn kitchen-sink dashboard, 3D canvas (phase 2).
