---
name: ui-ux-screening
description: Design system and UI rules for the oral health screening widget (mobile-first capture + report). Use when building or restyling web UI, landing, capture steps, report screen, Tailwind tokens, or visual polish. Distilled from ui-ux-pro-max patterns plus dental/health product constraints.
---

# UI/UX — oral screening widget

Source inspiration: [ui-ux-pro-max-skill](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill) (design intelligence only — we do not vendor their CLI).  
Also respect the user’s frontend design rules in the session (no generic AI purple, no cream+terracotta default, no broadsheet).

## Product UI shape

This is a **2-minute clinical-feeling widget**, not a SaaS marketing site and not a dashboard.

**Screens only (core):** Landing → Capture (×5) → Analyzing → Report.

## Design direction (locked for this project)

| Token | Choice |
|---|---|
| Mood | Clean clinical trust, calm, phone-first |
| Primary | Deep teal / medical blue-green (e.g. `#0F6B6B`) |
| Accent | Soft coral for CTA only (e.g. `#E07A5F`) — not purple |
| Background | Soft cool gray-white (`#F4F7F7`), subtle depth not flat void |
| Text | Near-ink `#1A2332` |
| Display font | Distinctive sans (e.g. **Plus Jakarta Sans** or **DM Sans**) — not Inter/Roboto/Arial |
| Body font | Same family or paired clean sans |

**Avoid:** purple-on-white AI gradients, dark neon cyber look, emoji-as-icons, card farms in the hero, floating badges on media.

## Capture UX (critical)

- One view at a time with a **silhouette / guide overlay** and short tip  
- Huge primary button: Capture / Retake / Next  
- Progress: `2 of 5` — plain, not a gimmick  
- Allow gallery upload as fallback when camera fails  
- Thumb-reach: primary CTA bottom-safe on mobile  

## Report UX

- Overall banner first: **Consider a visit** / **No obvious visual concern**  
- Then findings list: concern + confidence + which view  
- Show the five thumbnails  
- Persistent disclaimer: screening ≠ diagnosis  
- No fake tooth-number precision in core (unless phase-2 pins exist)

## Checklist before shipping UI

- [ ] Works at 375px width  
- [ ] `cursor-pointer` on clickable controls  
- [ ] Visible focus rings  
- [ ] `prefers-reduced-motion` respected  
- [ ] Contrast ≥ 4.5:1 for body text  
- [ ] Lucide/Heroicons SVG — no emoji icons  
- [ ] Loading and error states are plain and actionable  

## Copy tone

- “Start screening” not “Submit”  
- “Possible staining” not “Patient has tooth discoloration disease”  
- Errors say what to do: “Allow camera access or upload a photo”
