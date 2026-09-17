---
name: pytorch-oral-train
description: Train EfficientNet-B0 on the Kaggle Oral Diseases dataset for the screening widget. Use when writing train/, Kaggle notebooks, metrics, class_map, exporting best.pt, or discussing discoloration/crowding/wear labels.
---

# PyTorch training — oral diseases

## Dataset

Primary: [Oral Diseases (Kaggle)](https://www.kaggle.com/datasets/salmansajid05/oral-diseases)  
Classes include Tooth Discoloration + related visible conditions.  
Details: `docs/DATASETS.md`.

## Recipe (locked)

| Item | Value |
|---|---|
| Model | `timm.create_model('efficientnet_b0', pretrained=True, num_classes=N)` |
| Size | 224×224 |
| Split | Stratified ~70/15/15; dedupe if time allows |
| Augment | Flip, mild ColorJitter, slight rotation |
| Loss | CrossEntropy (+ class weights if imbalanced) |
| Optim | AdamW; lower LR for backbone, higher for head |
| Train | Kaggle GPU; 10–20 epochs; early stop on val macro-F1 |
| Export | `weights/best.pt` + `weights/class_map.json` |

## Report tag mapping

```
Tooth Discoloration -> discoloration
other disease classes -> other_visual_concern
```

Do **not** invent a wear class without labels. Crooked/crowding = phase-2 dataset only after `best.pt` exists.

## Deliverables for README / Q&A

- Train/val sizes  
- Val accuracy + macro F1  
- Confusion matrix image  
- Hardware (e.g. Kaggle T4)  
- “Not clinically validated” disclaimer  

## Anti-patterns

- Copying training notebooks from dental product repos into this git history as “ours”  
- Making the live demo depend on an unfinished run with no dummy fallback  
- Training ViT/Swin for the same demo — waste of time  

## Minimal train script shape

`ImageFolder` or explicit folder map → DataLoader → train/eval loop → save best state_dict + class index map. Prefer a plain `train/train.py` runnable on Kaggle or local GPU.
