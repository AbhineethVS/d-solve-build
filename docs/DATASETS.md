# Datasets & training

Public datasets and pretrained backbones are **allowed** under DSOLVE rules. We train our own head/checkpoint during the hackathon.

---

## Primary dataset (start here)

**Oral Diseases (Kaggle)**  
https://www.kaggle.com/datasets/salmansajid05/oral-diseases

- Intraoral / oral RGB photos  
- ~11k–12k images (depending on mirror/version)  
- Classes typically include: **Calculus, Caries, Gingivitis, Hypodontia, Mouth Ulcer, Tooth Discoloration**

### Why this one

- Directly covers PS1 **discoloration**  
- Extra visible conditions are still honest “visual concerns” for a screening report  
- Large enough for transfer learning in a few GPU hours on Kaggle  

### Care with metrics

Some mirrors have duplicates / leakage across folders. Before claiming accuracy:

1. Deduplicate if feasible (hash / perceptual hash)  
2. Stratified train / val / test split (e.g. 70/15/15)  
3. Report **val accuracy + macro F1 + confusion matrix** in README  

---

## Secondary (only after primary `best.pt` exists)

| Need | Option | Notes |
|---|---|---|
| Crooked / crowding | OMNI malocclusion dataset (paper + GitHub Drive) or Roboflow crowding sets | Separate small model or multi-label later |
| Wear / attrition | Weak / rare public phone-photo labels | **Do not invent a class.** Pitch as limitation |

---

## Model recipe (locked)

| Item | Choice |
|---|---|
| Backbone | EfficientNet-B0 via `timm` |
| Input | 224×224 RGB, ImageNet mean/std |
| Augment | Flip, mild color jitter, rotation |
| Loss | CrossEntropy (+ class weights if imbalance) |
| Optim | AdamW, low LR on backbone, higher on classifier |
| Epochs | ~10–20 with early stopping on val F1 |
| Export | `weights/best.pt` + `weights/class_map.json` |

Train on **Kaggle Notebooks GPU**. Export checkpoint to laptop for FastAPI.

---

## Class → report tags

```text
Tooth Discoloration  -> discoloration
Calculus             -> other_visual_concern
Caries               -> other_visual_concern
Gingivitis           -> other_visual_concern
Mouth Ulcer          -> other_visual_concern
Hypodontia           -> other_visual_concern
```

UI copy should prefer plain language (“possible staining”) over clinical certainty.

---

## What to put in the README after training

- Dataset name + link + license note  
- Train/val sizes  
- Best val accuracy / macro F1  
- Confusion matrix image  
- Hardware used (e.g. Kaggle T4)  
- Explicit: not clinically validated  

---

## Anti-patterns

- Forking a finished dental-AI GitHub app as the product  
- Claiming wear/crooked accuracy with no labeled data  
- Making the live demo depend on unfinished overnight training with no fallback fixture JSON  
