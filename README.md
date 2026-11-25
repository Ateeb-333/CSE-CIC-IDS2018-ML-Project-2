# CSE-CIC-IDS2018 Intrusion Detection — ML Semester Project

Replication and exploratory improvement experiments based on:

> W. H. Aljuaid and S. S. Alshamrani, "A Deep Learning Approach for Intrusion Detection Systems in Cloud Computing Environments," *Applied Sciences*, vol. 14, no. 13, art. 5381, 2024. DOI: [10.3390/app14135381](https://doi.org/10.3390/app14135381).

## Project overview

This project independently reconstructs a CNN-based network intrusion detection pipeline using the public **CSE-CIC-IDS2018** dataset and compares results across seven attack scenarios to those reported in the paper. Separate exploratory experiments study Scenario 6 (infiltration): CNN hyperparameter tuning, classification thresholds, feature ranking/selection, and Random Forest classification.

**Important:** This is an independent reconstruction, **not** an exact reproduction of the authors' source code or full experimental sample. It uses at most **12,000 observations per label** in `QUICK` mode (see the notebook). Changes to sampling, preprocessing, balancing, feature selection, validation/test protocol and model details may affect comparability. The publication year (2024) differs from the course manual's 2025–2026 requirement; an instructor exception is necessary.

## Dataset

- Dataset: **CSE-CIC-IDS2018** (University of New Brunswick / Canadian Institute for Cybersecurity).
- Official source: https://www.unb.ca/cic/datasets/ids-2018.html
- AWS Open Data: https://registry.opendata.aws/cse-cic-ids2018/
- Source download: 10 processed network-flow CSVs (approximately 6–7 GB). Do not commit the dataset to GitHub.
- Raw data processed in this project: **16,233,002 records**. Invalid rows removed: **95,819**. Cleaned records: **16,137,183**.

The notebook expects the CSVs in Google Drive at:

```text
/content/drive/MyDrive/CSE_CIC_IDS2018/Processed_Csv/
```

Intermediate data and model artifacts are written under `/content/drive/MyDrive/CSE_CIC_IDS2018/`.

## How to run

1. Open the notebook under [`notebooks/`](notebooks/) in [Google Colab](https://colab.research.google.com/).
2. Mount your Google Drive and ensure the 10 processed CSVs exist at the path above.
3. Run the notebook in sequence. It installs additional dependencies where required. Use a GPU runtime if available.
4. The initial pipeline verifies the CSV files, cleans and checkpoints Parquet outputs, inspects class distributions, and builds a deterministic class-capped sample.
5. The baseline section trains and evaluates the seven CNN scenarios; the later cells provide exploratory Scenario 6 experiments, including Random Forest.
6. Results are saved under `ML_Project_Outputs/Reports`, plots under `ML_Project_Outputs/Plots`, and model files under `ML_Project_Outputs/Models` on your Google Drive.

Changing `MODE`, sample size, seed, or model settings creates a different experiment; do not mix results from different settings. Models and full data are intentionally omitted from this GitHub repository due to size.

## Baseline CNN comparison

All figures are percent accuracy. These are **descriptive**, non-equivalent paper-to-project comparisons.

| Scenario | Paper accuracy | Reconstructed CNN accuracy |
|---|---:|---:|
| 1 — Brute force | 99.94 | 99.9537 |
| 2 — DoS | 99.95 | 99.9333 |
| 3 — DDoS LOIC-HTTP | 99.27 | 99.9444 |
| 4 — Advanced DDoS | 100.00 | 100.0000 |
| 5 — Web application attacks | 99.29 | 96.7517 |
| 6 — Infiltration | 92.43 | 65.1528 |
| 7 — Botnet | 100.00 | 99.8750 |

See `results/paper_vs_project_comparison.csv` for the complete table and `plots/` for training curves and confusion matrices.

## Exploratory Scenario 6 findings

- Improved CNN (20 epochs, dropout 0.3, lower learning rate): **66.21%** test accuracy (default threshold).
- Reduced feature set (15 features): **63.06% validation accuracy**, versus **65.12%** for the original 51-feature CNN on the same validation split; feature reduction was not beneficial.
- Random Forest (51 features, threshold 0.60): **70.26% exploratory test accuracy**, 58.39% infiltration recall, 66.26% infiltration F1, and 17.86% false-positive rate.
- Lower CNN decision thresholds raised infiltration recall but produced excessive false alarms; therefore these are not presented as general accuracy improvements.

**Caution:** The test set was examined during iteration. The later RF result is exploratory, not an unbiased independent final benchmark. The reduced-feature / RF validation findings also have a different evaluation subset from the original paper.

## Repository layout

```text
notebooks/       # Complete executable Google Colab notebook, including late experiments
results/         # CSV/JSON metrics and comparison outputs from the exported ZIP
plots/           # Figures exported by the notebook
reports/         # Reproducibility manifest (final write-up can be added here)
README.md        # Project overview, reproducibility and limitations
requirements.txt
.gitignore
```

## Additional Colab exports to add

The last six Scenario 6 CSV outputs were created **after** the initial `ML_Project_Results_Only.zip` export and are not in that ZIP. Copy these from `MyDrive/CSE_CIC_IDS2018/ML_Project_Outputs/Reports/` into `results/` before final GitHub submission:

- `scenario6_threshold_comparison.csv`
- `scenario6_feature_ranking.csv`
- `scenario6_feature_selection_validation.csv`
- `scenario6_cnn_vs_rf_validation.csv`
- `scenario6_rf_threshold_analysis.csv`
- `scenario6_rf_final_test.csv`

The notebook contains the code and displayed outcomes for these analyses, but the exported CSV files should be included for full traceability. The complete final semester report and paper approval form are separate deliverables.

## Methodological cautions and academic integrity

- The original article's scenario counts and some architecture details differ from this independent reconstruction; assumptions are recorded in `reports/reproducibility_manifest.json` and notebook markdown.
- Pearson correlation feature filtering uses a specified 0.98 threshold as an implementation assumption; fitting is restricted to training data.
- Standardization and SMOTE operate on training data only to avoid evaluation leakage.
- The label `Infilteration` is kept as it appears in the source CSV data.
- Cite the source paper, data provider and any external code or services. Review the notebook and be able to explain all code and results. Disclose AI assistance in the submitted report as required by the course manual.

## Citation

Aljuaid, W. H., & Alshamrani, S. S. (2024). A Deep Learning Approach for Intrusion Detection Systems in Cloud Computing Environments. *Applied Sciences, 14*(13), 5381. https://doi.org/10.3390/app14135381
