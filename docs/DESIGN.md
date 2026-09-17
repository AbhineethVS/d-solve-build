# Design — layout & UX (no fixed palette)

**For every agent building `web/` or shaping report UX in `api/`:** read this file first.  
**Colors, gradients, and exact fonts:** **not specified here** — the implementing agent chooses a coherent, clinical, mobile-first look. Do **not** default to generic purple-AI or cream+terracotta clichés.

This doc locks **structure, flow, hierarchy, and copy jobs** only.

Related: `docs/ARCHITECTURE.md` (API JSON) · `docs/guides/FRONTEND.md` · skill `ui-ux-screening` (layout/anti-patterns; ignore any hex if present — **this file wins on “no prescribed colors”**).

---

## 1. Product shell

- Single mobile-first widget (max readable column on desktop; content feels like a phone app).
- Working title: use repo/product name until brand is decided — one clear wordmark area on Landing.
- Always-visible idea: **screening, not diagnosis.**

---

## 2. Global layout rules

1. **One job per screen** — no dashboard chrome, no side nav.
2. **Four screens only (core):** Landing → Capture → Analyzing → Report.
3. **Primary action** lives in the **bottom thumb zone** (Start / Capture / Next / Start over).
4. **Progress** only on Capture: plain `n of 5` + view label — not a gimmick stepper with 5 icons.
5. **Disclaimer** on Landing and Report (and optionally a one-liner on Analyzing).
6. **Hierarchy:** title → short help → main visual/content → primary button → secondary links.
7. **Density:** airy enough for thumbs; don’t pack stats or marketing cards into Capture/Report.
8. **Motion:** optional and minimal; respect `prefers-reduced-motion`.
9. **Icons:** SVG only if needed — no emoji-as-UI.
10. **Wow** mounts only on Report (extra slot) — must not change Capture flow.

---

## 3. Screen inventory & wireframes

### S1 — Landing

```
┌─────────────────────────┐
│  [Brand]                │
│                         │
│  Headline               │
│  One supporting line    │
│                         │
│  (optional quiet visual │
│   — not a card grid)    │
│                         │
│  [ Start screening ]    │
│  Disclaimer text        │
└─────────────────────────┘
```

**Must include:** brand, promise (~2 minutes / 5 photos), Start, disclaimer.  
**Must not:** feature grids, pricing, chat entry, clinic map.

---

### S2 — Capture (repeats 5 times)

Views in order: `frontal` → `upper` → `lower` → `left` → `right`.

```
┌─────────────────────────┐
│  2 of 5 · Upper teeth   │
│  Short tip (one line)   │
│                         │
│  ┌───────────────────┐  │
│  │  Camera preview   │  │
│  │  + guide overlay │  │
│  └───────────────────┘  │
│                         │
│  [ Retake ] [ Capture ] │
│  Upload instead (link)  │
└─────────────────────────┘
```

**Must include:** progress, view name, tip, live preview or upload, Retake, Capture/Next.  
**Capture behavior:** **manual shutter** (user taps). No auto-fire required.  
**After Capture on steps 1–4:** advance to next view. **After 5:** go to Analyzing.  
**Fallback:** gallery/file upload if camera permission fails.

---

### S3 — Analyzing

```
┌─────────────────────────┐
│                         │
│      (status motion)    │
│   Checking your photos… │
│   Short honest line     │
│                         │
└─────────────────────────┘
```

**Copy job:** calm, non-clinical (“colour, alignment, and wear”) — not “diagnosing disease.”  
**No** fake progress percentages unless real.

---

### S4 — Report

```
┌─────────────────────────┐
│  Overall banner         │
│  (consider visit / OK)  │
│                         │
│  Findings               │
│  · Crooked …            │
│  · Wear …               │
│  · Discoloration …      │
│  · (other, if any)      │
│                         │
│  [Wow slot: map/evidence│
│   — optional]           │
│                         │
│  Five thumbnails        │
│                         │
│  Disclaimer             │
│  [ Start over ]         │
└─────────────────────────┘
```

**Finding row:** concern name → short note → confidence (if present) → optional `source` badge (`trained_model` / `vision_api`).  
**Order:** show **crooked, wear, discoloration** first when present; then `other_visual_concern`.  
**Thumbs:** the five captures; tap may open larger (Wow can enhance).  
**Must not:** raw model class dumps as the main UI (“Hypodontia 0.92”) without plain language.

---

## 4. Microcopy jobs (wording can vary; intent cannot)

| Place | Intent |
|---|---|
| Start | Begin the 5-photo flow |
| Capture | Take or retake this view |
| Upload instead | Camera fallback |
| Analyzing | Waiting on hybrid analyze |
| Overall consider_visit | Suggest seeing a dentist — not a diagnosis |
| Overall no_obvious_concern | No strong visual flags from these photos |
| Error camera | How to fix: allow permission or upload |
| Error network | Retry / check connection |

---

## 5. Backend-facing layout contract

BE does not pick colors. BE **must** enable this layout:

1. `POST /analyze` returns shape in `docs/ARCHITECTURE.md`.  
2. Prefer `summary[]` entries the Report can list in PS1 order.  
3. Always include `disclaimer` string.  
4. `source` on findings when known (UI may show a quiet badge).  
5. Field names for uploads stay: `frontal`, `upper`, `lower`, `left`, `right`.  
6. Dummy mode is fine — same shape so FE layout doesn’t fork.

---

## 6. Visual freedom (for the coding agent)

You **should** choose:

- A calm clinical palette and typography that fit oral-health screening  
- Spacing, radius, and elevation that keep primary CTA obvious  

You **must not**:

- Break the four-screen structure or Capture order  
- Add screens (auth, chat, map) in core  
- Prescribe or require hex values from older docs — **layout here overrides palette lists elsewhere**

---

## 7. Out of scope for this doc

Exact hex colours, logo SVG, full marketing site, dark mode spec, auto-capture shutter, 3D implementation details (see `docs/guides/WOW.md`).
