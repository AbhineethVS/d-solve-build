# Trainer guide (Train)

You own **training** — Kaggle Notebook (preferred for this dataset) or Colab GPU. Deliver `best.pt` + metrics. You do **not** block FE/BE; they use dummy + vision API until you finish.

**Dataset:** https://www.kaggle.com/datasets/salmansajid05/oral-diseases  
**Model:** EfficientNet-B0 (`timm`) · Skill: `pytorch-oral-train` · See `docs/DATASETS.md`

---

## Your job in one line

Fine-tune a classifier that detects **Tooth Discoloration** (and other Oral Diseases classes), export weights BE can load for the hybrid pipeline.

**Remember:** Crooked + wear are **not** your job (vision API). Don’t chase those labels in this dataset.

---

## Before training

1. Kaggle account + **phone verification** (required for GPU).  
2. Open dataset page → **Code** → **New Notebook** → attach Oral Diseases → Accelerator **GPU**.  
3. Tell BE when you expect first `best.pt` (even a rough one).  

---

## Step-by-step

### Step T1 — See the data (15–30 min)

- [ ] List input folders; note class names (Calculus, Caries, Gingivitis, Tooth Discoloration, Ulcer, Hypodontia, …)  
- [ ] Count images per class  
- [ ] Decide root path(s) if the zip has nested folders  

**Done when:** you know exact folder paths for `ImageFolder` or a custom loader.

### Step T2 — Environment (15 min)

- [ ] Confirm GPU: `torch.cuda.is_available()` → `True`  
- [ ] Install: `timm`, etc. as needed in the notebook  

### Step T3 — Dataset + split (45–90 min)

- [ ] Resize 224, ImageNet normalize  
- [ ] Augment: flip, mild color jitter, small rotation  
- [ ] Stratified train/val (e.g. 80/20 or 70/15/15)  
- [ ] Optional: quick duplicate check if time  

**Done when:** DataLoaders iterate without error.

### Step T4 — Model (30 min)

- [ ] `timm.create_model('efficientnet_b0', pretrained=True, num_classes=N)`  
- [ ] Class weights if imbalance  
- [ ] AdamW; lower LR on backbone, higher on head  

### Step T5 — Train (1–3 hrs wall clock)

- [ ] 10–20 epochs, early stop on val macro-F1 or accuracy  
- [ ] Save **best** checkpoint each improvement  
- [ ] Print classification report + confusion matrix  

**Done when:** you have `best.pt` and numbers you’re willing to say in Q&A.

### Step T6 — Export for Backend (30 min)

- [ ] Save `best.pt` (state_dict or full — **agree format with BE**)  
- [ ] Save `class_map.json`: `{ "0": "Tooth Discoloration", ... }` matching training indices  
- [ ] Download from Kaggle output / Drive  
- [ ] Drop into repo `weights/` **locally** (gitignored) or Drive link for the team  
- [ ] Paste into chat: val accuracy, macro-F1, epochs, GPU type  

### Step T7 — README blurb (20 min)

- [ ] Dataset link + train/val sizes  
- [ ] Metrics + confusion matrix image  
- [ ] “Not clinically validated”  
- [ ] Mapping: discoloration → PS1; other classes → other_visual_concern  

---

## When (timeline)

| Window | You should be on |
|---|---|
| Tonight | T1–T5 started; first epoch numbers in chat |
| Overnight | Let it train; save best often (session can die) |
| Fri morning | T6 delivered to BE |
| Fri | Optional second run if metrics are poor; else help BE test |

---

## If GPU is blocked

- Finish phone verify on Kaggle  
- Fallback: Colab GPU + Kaggle API download of the same dataset  
- Last resort: BE runs vision-only until you get a machine  

---

## Out of scope

Frontend, fusion logic, vision API prompts, wow UI, claiming wear/crooked accuracy from this checkpoint.
