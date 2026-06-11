# TCGA Pan-Cancer Classification

**University of Groningen — Year 3 Group Project (2025–2026)**

A machine learning pipeline for classifying 32 cancer types from RNA-seq gene expression data using The Cancer Genome Atlas (TCGA) dataset.

---

## Results

| Model | Test Accuracy | CV Accuracy (5-fold) |
|---|---|---|
| **Logistic Regression** | **96.2%** | 95.7% ± 0.6% |
| Random Forest | 94.5% | 93.5% ± 0.4% |
| SVM (RBF kernel) | 93.8% | 93.1% ± 0.2% |

- **9,185** primary tumour samples across **32** cancer types
- **60,498** gene expression features (Ensembl IDs)
- Trained and evaluated on the **Hábrók HPC cluster**, University of Groningen

---

## 🌐 Interactive Demo

Try the gene explorer here — enter any Ensembl gene ID to see which cancer types it predicts:

👉 **[TCGA Cancer Gene Explorer](https://f9mhw6z88z-sketch.github.io/tcga-cancer-explorer)**

---

## Repository Structure

```
tcga-cancer-explorer/
├── index.html                          # Interactive gene explorer web app
├── tcga_fixed_methodology.py           # Main analysis script (fixed methodology)
├── generate_figures.py                 # Figure generation script
├── appendix_top5_genes_per_cancer.csv  # Top 5 ANOVA genes per cancer type
├── feature_importance.csv              # Random Forest gene importances
├── requirements.txt                    # Python dependencies
└── README.md                           # This file
```

---

## Running the Analysis

### Requirements
```bash
pip install -r requirements.txt
```

### Run on your machine
```bash
python3 tcga_fixed_methodology.py path/to/merged.csv
```

### Run on HPC (SLURM)
```bash
sbatch run_tcga.sh
```

---

## Methodology

### Data
- Source: [TCGA via UCSC Xena Browser](https://xenabrowser.net/)
- Data type: RNA-seq RSEM gene TPM (log2-transformed)
- Filter: Primary Tumor samples only
- Original: 10,534 samples × 60,503 columns → 9,185 samples × 60,498 gene features after filtering

### Key fixes vs naive implementation
1. **Data leakage fix** — StandardScaler wrapped in `sklearn.Pipeline` so it is re-fit on each CV fold independently
2. **Real gene IDs** — Actual Ensembl IDs used in feature importance, not placeholder names
3. **5-fold CV** — Increased from 3-fold to 5-fold for more reliable generalisation estimates
4. **Per-cancer gene analysis** — ANOVA F-tests (one-vs-rest) to identify top 5 genes per cancer type

### Models
All models implemented with `scikit-learn` (v1.8.0) inside `Pipeline` objects:
- **Logistic Regression** — L-BFGS solver, 5,000 max iterations, StandardScaler
- **Random Forest** — 100 trees, max depth 20, no scaling
- **SVM** — RBF kernel, C=1.0, StandardScaler

---

## Computational Resources

Trained on the **Hábrók HPC Cluster**, University of Groningen:
- Node: omni4 | 8 CPU cores | 64 GB RAM allocated
- Peak memory: 51.76 GB
- Total runtime: 50 minutes 16 seconds

---

## Key Findings

- Logistic Regression outperformed more complex models, consistent with literature showing linear classifiers are effective on high-dimensional transcriptomic data
- 7 cancer types achieved perfect F1 = 1.00 (thyroid, prostate, ovarian, testicular, thymoma, glioblastoma, pheochromocytoma)
- Lowest performance: rectum adenocarcinoma (F1 = 0.53) — confused with colon adenocarcinoma due to shared transcriptomic profiles
- Class imbalance (36–1,092 samples per type) was not corrected via oversampling — identified as a limitation

---

## Requirements

```
pandas>=1.3.0
numpy>=1.21.0
matplotlib>=3.5.0
seaborn>=0.11.0
scikit-learn>=1.0.0
joblib>=1.1.0
```

---

## 👥 Authors

Group project — University of Groningen, BSc programme, Year 3 (2025–2026)
Nikta Yazdani, Erika Sonko, & Ollie Wingfield

---

## Acknowledgements

Computational resources provided by the **Hábrók High Performance Computing cluster**, University of Groningen.
Data from **The Cancer Genome Atlas (TCGA)** accessed via the **UCSC Xena Browser**.
