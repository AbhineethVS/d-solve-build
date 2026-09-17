---
name: frontend-design
description: Guidance for distinctive, intentional visual design when building new UI. Use when designing pages, choosing typography/color, or avoiding generic AI-looking layouts. Adapted from Anthropic agent skills frontend-design.
---

# Frontend Design

Approach UI as a design lead paid for a distinct point of view — not a templated default.

## Ground in the subject

This product is **oral health screening on a phone**. Vernacular: clinical calm, clarity, trust. Not fintech neon, not toy pastel, not generic SaaS purple.

## Principles

- One memorable visual idea; keep the rest quiet  
- Typography carries personality — pick intentional faces, not Inter/Roboto/system defaults  
- Motion: prefer response to user action; avoid fade-up-every-section  
- Copy is design: plain verbs, sentence case, one job per string  

## AI-default looks to avoid (unless the brief demands them)

1. Warm cream `#F4F1EA` + terracotta serif  
2. Near-black + acid green / vermilion only  
3. Broadsheet hairline newspaper columns  
4. Identical rounded card grid + soft grey shadows everywhere  
5. ALL-CAPS eyebrows, middle-dot meta rows, purple gradients  

## Process

1. Write a short token plan (color, type, layout, principles)  
2. Critique it for “would I generate this for any app?” — revise if yes  
3. Then code  

For this repo, prefer tokens in `ui-ux-screening` when they conflict with free exploration.
