# Fraud Detection — Machine Learning on Cloud (LD7187)

---

### Project Summary

This project implements a complete machine learning pipeline for credit card fraud detection, deployed on **Google Cloud Platform (GCP) Vertex AI Workbench**.

| Item | Detail |
|------|--------|
| Dataset | Kaggle Credit Card Fraud Detection (284,807 transactions, 0.17% fraud) |
| Platform | GCP Vertex AI Workbench (n1-standard-8, europe-west2) |
| Data storage | Google Cloud Storage |
| Best model | Random Forest, XGBoost |

---

### Tasks Covered

| Task | Description | Notebook Cells |
|------|-------------|----------------|
| Task 1 | Cloud feasibility study (covered in report) | — |
| Task 2 | EDA — distributions, heatmaps, KDE, boxplots | Cells 2–7 |
| Task 3 | Preprocessing — dedup, feature engineering, scaling, SMOTE | Cells 8–15 |
| Task 4 | Model training — LR, RF, XGBoost, MLP; CV + hyperparameter tuning | Cells 16–21 |
| Task 5 | Evaluation — confusion matrices, ROC/PR curves, feature importance | Cells 22–25 |

---

### Files

| File | Description |
|------|-------------|
| `ML_Fraud_Detection_GCP.ipynb` | Main notebook — GCP/Vertex AI version |
| `requirements.txt` | Python dependencies |
| `GCP_DEPLOYMENT_GUIDE.md` | Step-by-step GCP deployment instructions |

---

### How to Run

1. Follow `GCP_DEPLOYMENT_GUIDE.md` to set up Vertex AI Workbench and upload the dataset to GCS
2. Open `ML_Fraud_Detection_GCP.ipynb` in JupyterLab
3. Update `GCS_BUCKET` in Cell 0 to your bucket name
4. Run all cells (**Kernel → Restart and Run All**)

Expected runtime: **15–25 minutes** on n1-standard-8 (vs 60+ min on Colab)

---

### Dependencies

```
pandas, numpy, matplotlib, seaborn, scikit-learn,
imbalanced-learn, xgboost, scipy, google-cloud-storage
```

---
