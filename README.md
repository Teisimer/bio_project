# Conditional GAN for Synthetic Gene Expression Data Generation

This repository contains the complete pipeline for generating and evaluating synthetic gene expression data using deep generative models including:
- Variational Autoencoder (VAE)
- Generative Adversarial Network (GAN)
- Conditional GAN (CGAN)

The dataset used in this project is derived from [GEO Series GSE126848](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE126848), which profiles hepatic transcriptome signatures in patients with various stages of non-alcoholic fatty liver disease (NAFLD) and control groups (Normal and Obese).

---

## 📂 Project Structure

```bash
├── data/
│   ├── GSE126848_series_matrix.txt
│   ├── GSE126848_Gene_counts_raw.txt
│   └── GSE126848_Gene_counts_keyname.txt  # Preprocessed mapping
├── preprocessing/
│   └── preprocess_labels_and_counts.py
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

## 🗂️ How to Use

1. Clone the repository:
```bash
git clone https://github.com/yourusername/CGAN-GeneExpression.git
cd CGAN-GeneExpression
```

2. Setup environment:
```bash
pip install -r requirements.txt
```

3. Preprocess the data:
```bash
python preprocessing/preprocess_labels_and_counts.py
```

4. Train the models:
```bash
python training/train_vae.py
python training/train_gan.py
python training/train_cgan.py
```

5. Visualize outputs:
```bash
python visualization/pca_tsne_visualize.py
```

---

## 📌 Results Highlights

- CGAN shows superior class-targeted generation (e.g., NAFL/NASH clustering)
- VAE generated samples maintain overall structure but lower specificity
- Visual inspections via PCA/t-SNE show improved diversity in GAN-generated samples

---
