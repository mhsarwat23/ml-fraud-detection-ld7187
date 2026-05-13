# GCP Deployment Guide
## Machine Learning on Cloud — Fraud Detection Project (LD7187)

---

## Overview

This guide explains how to run the fraud detection notebook on **Google Cloud Platform (GCP)** using **Vertex AI Workbench**, replacing the slow Google Colab environment. The dataset (`creditcard.csv`) is stored in **Google Cloud Storage (GCS)** and loaded automatically by the notebook.

---

## Architecture

```
creditcard.csv  →  GCS Bucket  →  Vertex AI Workbench Notebook  →  Trained Model  →  GCS (saved)
                                         ↑
                                  High-RAM VM (n1-standard-8)
                                  8 vCPUs, 30GB RAM
```

---

## Step 1: Create a GCP Project

1. Go to [https://console.cloud.google.com](https://console.cloud.google.com)
2. Click **"New Project"** → give it a name (e.g. `ml-fraud-detection`)
3. Note your **Project ID** — you'll need it throughout
4. Enable billing (use the $300 free trial credit if available)

---

## Step 2: Enable Required APIs

In the GCP Console, navigate to **APIs & Services → Library** and enable:

- **Vertex AI API**
- **Cloud Storage API**
- **Notebooks API**

Or run in Cloud Shell:
```bash
gcloud services enable aiplatform.googleapis.com storage.googleapis.com notebooks.googleapis.com
```

---

## Step 3: Upload Dataset to Google Cloud Storage

### Option A — GCP Console (easiest)
1. Go to **Cloud Storage → Buckets → Create Bucket**
2. Name: `ml-cloud-fraud-2025` (must be globally unique — add your initials if needed)
3. Region: `europe-west2` (London — closest to you)
4. Storage class: **Standard**
5. Click **Create**
6. Open the bucket → **Upload Files** → select `creditcard.csv`
7. Upload to the path: `data/creditcard.csv`

### Option B — Cloud Shell
```bash
# Create bucket
gsutil mb -l europe-west2 gs://ml-cloud-fraud-2025

# Upload CSV
gsutil cp creditcard.csv gs://ml-cloud-fraud-2025/data/creditcard.csv

# Verify
gsutil ls gs://ml-cloud-fraud-2025/data/
```

---

## Step 4: Create a Vertex AI Workbench Notebook

1. Go to **Vertex AI → Workbench → User-Managed Notebooks**
2. Click **"+ New Notebook"**
3. Select **Python 3 (with Intel MKL)**
4. Configure the machine:
   - **Machine type**: `n1-standard-8` (8 vCPUs, 30 GB RAM) — handles SMOTE + GridSearchCV fast
   - **Region**: `europe-west2`
   - **Zone**: `europe-west2-a`
5. Leave GPU as **None** (not needed for this workload)
6. Click **Create** — wait ~3 minutes for it to start

> **Cost estimate**: n1-standard-8 costs ~$0.38/hour. A full notebook run takes ~15–30 min, so the total cost is under $0.20.

---

## Step 5: Upload the Notebook

### Option A — Workbench UI
1. Once the notebook instance shows **"Open JupyterLab"**, click it
2. In JupyterLab → click the **Upload** button (↑ icon) in the file browser
3. Upload `ML_Fraud_Detection_GCP.ipynb`

### Option B — gcloud CLI
```bash
# From your local machine:
gcloud notebooks instances register MY_INSTANCE \
  --location=europe-west2-a \
  --project=YOUR_PROJECT_ID

# Then copy file via gcloud compute scp:
INSTANCE_NAME="your-notebook-instance-name"
gcloud compute scp ML_Fraud_Detection_GCP.ipynb \
  ${INSTANCE_NAME}:/home/jupyter/ \
  --zone=europe-west2-a
```

---

## Step 6: Configure the Notebook

Open `ML_Fraud_Detection_GCP.ipynb` in JupyterLab.

In the **first cell (GCP Setup)**, update these two lines:

```python
GCS_BUCKET = 'ml-cloud-fraud-2025'    # ← your bucket name
GCS_BLOB   = 'data/creditcard.csv'    # ← path inside bucket (don't change if you followed Step 3)
```

---

## Step 7: Run the Notebook

1. In JupyterLab: **Kernel → Restart Kernel and Run All Cells**
2. The first cell installs packages and downloads the CSV — takes ~2 minutes
3. All subsequent cells run as normal — expected total runtime: **15–25 minutes** on n1-standard-8

> On Colab, the GridSearchCV cells (Cells 18–19) can take 30–60+ minutes. On Vertex AI with 8 cores and `n_jobs=-1`, they run in under 10 minutes.

---

## Step 8: Save Output

The final cell saves the best model to GCS:
```
gs://ml-cloud-fraud-2025/models/fraud_rf_model.pkl
```

You can verify it was saved:
```bash
gsutil ls gs://ml-cloud-fraud-2025/models/
```

---

## Step 9: Screenshot Evidence for Report

Take screenshots of:
- [ ] The GCS bucket with `creditcard.csv` uploaded
- [ ] The Vertex AI Workbench instance running
- [ ] Cell 1 output (Shape: (284807, 31))
- [ ] Cell 16 output (Baseline model training)
- [ ] Cell 21 output (Tuned model comparison table)
- [ ] Cell 22–23 (Confusion matrices, ROC curves)
- [ ] The GCS bucket showing `models/fraud_rf_model.pkl`

---

## Step 10: Stop the Instance (Important!)

After your run is complete, **stop the notebook instance** to avoid charges:

```bash
gcloud notebooks instances stop YOUR_INSTANCE_NAME \
  --location=europe-west2-a
```

Or in the Console: **Vertex AI → Workbench → Stop**.

---

## Troubleshooting

| Issue | Fix |
|-------|-----|
| `google.cloud.storage not found` | First cell installs it; restart kernel if needed |
| `Permission denied` on GCS | Make sure notebook uses the project's default service account |
| `Out of memory` on SMOTE | Upgrade to `n1-highmem-8` (52 GB RAM) |
| Packages won't install | In JupyterLab terminal: `pip install -r requirements.txt` |

---

## File Structure (submit with report)

```
submission/
├── ML_Fraud_Detection_GCP.ipynb   ← main notebook (GCP version)
├── requirements.txt               ← dependencies
├── GCP_DEPLOYMENT_GUIDE.md        ← this file
└── README.md                      ← code link & summary
```

---

*Module: LD7187 — Machine Learning on Cloud | Submission deadline: 21 May 2026*
