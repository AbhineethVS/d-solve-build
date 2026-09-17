# Wow guide

You own the **visual wow** on top of the screening report — not a second product.

**Depends on:** stable report JSON from FE+BE (fixture is enough to start).  
**Do not block:** capture → `/analyze` → basic report.

---

## Your job in one line

Make the report memorable: **evidence on the photo** and/or a **simple visual dental map**, using the same findings FE already shows.

---

## Priority order (stop when time runs out)

1. **Evidence highlight** — when user taps a finding, zoom/crop or outline the source view thumbnail  
2. **2D dental map** — anterior arch diagram with pins for findings (tooth hints by view are OK; perfect FDI optional)  
3. **Light motion** — one purposeful reveal on report (respect `prefers-reduced-motion`)  
4. **3D arch (R3F)** — only if 1–2 work and core demo is green  

---

## Step-by-step

### Step W1 — Contract (30 min)

- [ ] Get fixture JSON from BE/FE  
- [ ] List fields you’ll use: `summary[].concern`, `views`, `note`, image blobs from FE state  
- [ ] Agree with FE where your components mount (report page slot)

### Step W2 — Evidence panel (2–4 hrs)

- [ ] Tap finding → show related capture large  
- [ ] Optional box if BE later adds `evidence_box`; until then full-frame + label is fine  

### Step W3 — Visual map (3–6 hrs)

- [ ] Static SVG/PNG arch or simple CSS layout for anterior teeth  
- [ ] Pins for discoloration / crooked / wear  
- [ ] Tap pin → same as finding detail  

### Step W4 — Integrate (1–2 hrs)

- [ ] PR into `web/` with FE review  
- [ ] Must work on phone width  

---

## When

| Window | You |
|---|---|
| Tonight | W1 + help FE polish if JSON not ready |
| After dummy report works | W2 |
| Fri if core green | W3 |
| Only if ahead | W4 3D |

---

## Out of scope

Chatbot, clinic locator, training, changing fusion rules, Flutter.
