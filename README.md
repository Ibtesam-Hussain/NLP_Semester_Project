# NLP Course Project — README

##  Overview
A Group Project ME and fellow friends 
22K4039 Safey Ahmed
22K8719 Shaheer Uddin
22K8720 Ayan Khan
This repository contains an exploratory and modeling notebook for Code-Mixed Urdu–English sentiment/emotion analysis. The main work is in `NLP_Project.ipynb`, and this README summarizes what the notebook does, how to reproduce experiments, and what fixes were made to make the notebook render properly on GitHub.

---

##  Notebook structure & key steps
1. Data acquisition
   - Downloaded the "roman Urdu–English code-switched emotion dataset" using `kagglehub`.
   - Loaded the dataset from the Excel file: `RU-EN-Emotion Dataset.xlsx`.

2. Data cleaning & normalization
   - Dropped unnecessary columns and rows with missing `Text` or `Emotion` values.
   - Converted column names for consistency (e.g., `Tweets` → `Text`, `Level 2` → `Emotion`).
   - Implemented `clean_text(text)` to:
     - Lowercase, remove URLs/mentions/hashtags
     - Remove emojis
     - Strip non-letter characters
     - Reduce letter elongation and extra spaces
   - Implemented `normalize_roman_urdu(text)` using a `normalization_dict` to map common Roman Urdu spellings and slang to canonical forms.
   - Added `clean_text` and `normalized_text` columns to the dataframe.

3. Labels & splits
   - Created a label mapping (`label_map`) from emotion string labels to integers.
   - Performed stratified train/test split (80/20) to preserve class balance.

4. Baseline model: XLM-Roberta fine-tune
   - Tokenization: `XLMRobertaTokenizer.from_pretrained('xlm-roberta-base')` with `max_length=128`.
   - Dataset: converted Pandas DataFrames to `datasets.Dataset`, mapped tokenization and set format for PyTorch.
   - Model: `XLMRobertaForSequenceClassification` with `num_labels` set to the number of classes.
   - TrainingArguments: epochs, batch sizes, learning rate, logging and save settings were configured.
   - Metrics: custom `compute_metrics` returning accuracy, precision, recall, and F1 (weighted).
   - Results reported from evaluation (example metrics were shown in the notebook).
   - Saved model and tokenizer under `./xlmr_baseline_model`.

5. Secondary experiment: LID-aware / custom embeddings
   - Notebook contains code scaffolding for implementing a language-identifier-aware variant by combining XLM-R with custom language embeddings and additional preprocessing.
   - This section includes pronoun/auxiliaries/verbs/adjectives lists and extra normalizations used during experiments.

---

##  Notebook fixes & maintenance (IMPORTANT — GitHub rendering issue)
During troubleshooting, the notebook failed to render on GitHub with the error:

> "the 'state' key is missing from 'metadata.widgets'. Add 'state' to each, or remove 'metadata.widgets'."

I performed the following safe, reversible fixes locally to ensure GitHub can render the notebook:
- Added a missing `state` key where safe (initial check & fix). Backed up original file as `NLP_Project.ipynb.bak`.
- Found cells and outputs where `source` or output `text/data` was stored as lists rather than strings — converted these lists to strings and saved a backup `NLP_Project.ipynb.listsrc.bak`.
- Removed top-level `metadata.widgets` to avoid widget rendering issues (backup: `NLP_Project.ipynb.nowidgets.bak`).
- Verified `nbformat.validate()` — passed.
- Verified that `nbconvert.HTMLExporter` can now convert the notebook locally without raising the previous error.

Backups created (kept locally):
- `NLP_Project.ipynb.bak`
- `NLP_Project.ipynb.listsrc.bak`
- `NLP_Project.ipynb.nowidgets.bak`

To reduce noise in the repository, I added `*.bak` to `.gitignore`, then stopped tracking and removed the tracked backup files from the repository using:

```bash
git rm --cached project_report.tex NLP_Project.ipynb.listsrc.bak NLP_Project.ipynb.nowidgets.bak
git add .gitignore
git commit -m "Stop tracking project_report.tex and .bak files; add *.bak to .gitignore"
git push
```

---

##  Files and artifacts
- `NLP_Project.ipynb` — Main notebook (cleaned & fixed for GitHub rendering)
- `xlmr_baseline_model/` — Saved model & tokenizer (if training executed)
- Backup files (kept locally): see above
- `README.md` — This file

---

##  Environment & reproducibility
Recommended Python environment:
- Python 3.9+ (tested with the environment on the machine used)
- Main packages (as used in notebook):
  - pandas, numpy, tqdm, transformers, datasets, emoji, clean-text, unidecode, scikit-learn, torch, kagglehub

Install commands (example):
```bash
python -m pip install -r requirements.txt
# or
pip install kagglehub pandas numpy tqdm transformers datasets emoji clean-text unidecode scikit-learn torch
```

Run & validate the notebook locally:
```bash
# Validate nbformat
python - <<'PY'
import nbformat
nb = nbformat.read('NLP_Project.ipynb', as_version=4)
nbformat.validate(nb)
print('nbformat: ok')
PY

# Test nbconvert HTML export
python - <<'PY'
from nbconvert import HTMLExporter
import nbformat
nb = nbformat.read('NLP_Project.ipynb', as_version=4)
exp = HTMLExporter()
body, resources = exp.from_notebook_node(nb)
print('nbconvert: ok, bytes=', len(body))
PY
```

---

##  Notes & recommendations
- If you plan to keep interactive widgets outputs in the notebook (e.g., from ipywidgets), export or clear widget outputs before pushing to GitHub or make sure `metadata.widgets` contains valid `state` entries for each widget manager entry.
- Keep the `.bak` pattern in `.gitignore` to avoid accidentally committing backups. Backups are helpful, but shouldn't be tracked.



