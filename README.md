# Conditional GAN for Synthetic Gene Expression Data Generation

This repository contains the complete pipeline for generating and evaluating synthetic gene expression data using deep generative models including:
- Variational Autoencoder (VAE)
- Generative Adversarial Network (GAN)
- Conditional GAN (CGAN)

The dataset used in this project is derived from [GEO Series GSE126848](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE126848), which profiles hepatic transcriptome signatures in patients with various stages of non-alcoholic fatty liver disease (NAFLD) and control groups (Normal and Obese).

## 📁 Dataset 說明

本專案使用的資料集為 GEO 系統中的 **GSE126848**，其探討了 **非酒精性脂肪肝疾病（NAFLD）** 不同階段下的人體肝臟轉錄體表現，包含 4 類樣本：

- Normal（正常體重）
- Obese（肥胖）
- NAFL（脂肪肝）
- NASH（脂肪性肝炎）

本研究依據以下檔案進行資料處理與模型輸入：

| 檔名 | 功能說明 |
|------|----------|
| `GSE126848_series_matrix.xlsx` | 原始 meta 資料，包含樣本編號、分類與基本描述，用於建立樣本與類別對應表 |
| `GSE126848_Gene_counts_raw.xlsx` | GEO 提供的 RNA-seq 原始讀數表，每一列為基因、每欄為樣本 |
| `GSE126848_Gene_counts_keyname.xlsx` | 根據 `series_matrix` 對 `raw` 表欄位重新命名為分類名稱，利於後續統計與建模 |
| `converted_counts_data.csv` | 使用 `log2(CPM + 4)` 正規化轉換後的輸出資料，為主模型的輸入資料之一 |
| `processed_data.csv` | 透過 iDEP 平台篩選低表現基因後的結果，使用 Min CPM = 0.5, library ≥ 1 條件處理，最後模型的輸入資料 |
---

## 📂 Project Structure

```bash
├── data/
│   ├── GSE126848_series_matrix.txt
│   ├── GSE126848_Gene_counts_raw.txt
│   └── GSE126848_Gene_counts_keyname.txt  
│   └── processed_data.csv # final input data
├── models/
│   ├── vae
│   ├── gan
│   └── cgan
```

---

## 📊 Dataset Summary

- Source: GEO GSE126848
- Samples: 57 patients total
  - Normal (n=14)
  - Obese (n=12)
  - NAFL (n=15)
  - NASH (n=16)
- Gene expression measured by RNA-seq and provided as raw counts.

---

## 🧪 Preprocessing Steps

1. Parse series matrix metadata and map sample IDs to human-readable group names.
2. Normalize the gene count matrix using [iDEP](http://bioinformatics.sdstate.edu/idep/) with:
   - CPM threshold: `0.5`
   - Log2(CPM+4) transformation
3. MinMaxScaler is used for neural network input standardization.

---

## 🔧 Models

### Variational Autoencoder (VAE)
- Encoder: 256 → 128 → 64 → (z_mean, z_log_var)
- Latent dimension: 10
- Decoder: 64 → 128 → 256 → Output
- Loss: Reconstruction (MSE) + β-weighted KL divergence

### GAN
- Generator: 100 → 64 → 128 → 256 → 3004
- Discriminator: 3004 → 256 → 128 → 64 → 1
- Loss: Binary cross-entropy with logits

### Conditional GAN (CGAN)
- Generator: [noise + label] → 64 → 128 → 256 → 3004
- Discriminator: [data + label] → 256 → 128 → 64 → 1
- Label-conditional one-hot vectors for class-specific sample generation

---

## 📈 Visualizations

- PCA and t-SNE plots to visualize the generated samples' distribution compared with real data
- Evaluation across each class (Normal, Obese, NAFL, NASH)
- Sample-specific generation for targeted augmentation

---

## 📌 Results Highlights

- CGAN shows superior class-targeted generation (e.g., NAFL/NASH clustering)
- VAE generated samples maintain overall structure but lower specificity
- Visual inspections via PCA/t-SNE show improved diversity in GAN-generated samples

---
