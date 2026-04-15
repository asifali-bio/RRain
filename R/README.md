## `R/` Directory Overview

The `R/` directory contains all analysis workflows and precomputed data used in **RRain**, organized by:

- **data type**: `bulk` vs `sc` (single-cell)
- **feature space**: `go` vs `pfam`

This results in the following structure:

```
R/
├── bulk/
│ ├── go/
│ └── pfam/
└── sc/
├── go/
└── pfam/
```

---

## 🔷 `bulk/` — Bulk / Cross-sample Analysis

These folders contain workflows derived from the **PLANT framework**, where each sample is treated as a single entity (i.e., one “tower” per sample).

### Structure

```
R/bulk/go/
R/bulk/pfam/
```


### Contents

Each folder contains:

- **R script(s)**
  - The full workflow used to process and visualize the data

- **`*.RData` file**
  - `go.RData` or `pfam.RData`
  - Precomputed data objects generated from the PLANT pipeline
  - Included here for convenience (copied from the PLANT repository)

### Notes

- These datasets are static snapshots of bulk functional profiles.
- They demonstrate how domain/GO aggregation behaves at the **sample level**, rather than per cell.
- No external preprocessing is required to reproduce results—data is already embedded.

---

## 🔷 `sc/` — Single-cell Analysis (RRain)

These folders contain the core **RRain workflows**, where each cell becomes a “tower” in 3D space.

### Structure

```
R/sc/go/
R/sc/pfam/
```


### Contents

Each folder contains:

#### 1. Input data

- **`3K.RData`**
  - Seurat object containing the **PBMC 3K dataset**
  - Generated from 10x Genomics data (download + preprocessing included in script)

- **`*_output.tsv`**
  - `pfam_output.tsv` or `go_output.tsv`
  - Output from **InterProScan** on the reference human proteome
  - Provides mapping:
    ```
    protein → domain (Pfam or GO)
    ```

---

#### 2. Processing script

- **`Rain.R`**
  - Main RRain workflow (can be run line-by-line)
  - Performs:

    - Mapping:
      ```
      gene → protein → domain
      ```

    - Aggregation:
      ```
      cells × genes → cells × domains
      ```

    - Filtering and normalization

    - PCA embedding in **domain space**

    - Optional cell type annotation (Azimuth toggle)

---

#### 3. Output data

- **`3D.RData`**
  - Final processed dataset used for visualization
  - Stored as a **long-format table**:

    | column     | description |
    |------------|------------|
    | `cell`     | cell barcode |
    | `domain`   | domain ID (Pfam/GO) |
    | `x, y`     | PCA coordinates |
    | `z`        | domain index |
    | `tpm`      | domain-level expression |
    | `cluster`  | Seurat cluster |
    | `celltype` | predicted or fallback label |

---

## 🔁 Workflow Summary

Across both `bulk/` and `sc/`, the core transformation is:

```
genes → protein domains → aggregated functional space
```


- **bulk/** → one profile per sample
- **sc/** → one profile per cell (RRain towers)

The single-cell pipeline extends the bulk idea described in the main repo by embedding cells and stacking their domain composition vertically.

---

## ▶️ How to Run

For any `sc/` or `bulk/` subfolder:

1. Open the corresponding `Rain.R` (or bulk script)
2. Run line-by-line in R / RStudio
3. Ensure required packages are installed:
   - `Seurat`
   - `biomaRt`
   - `dplyr`, `tidyr`
   - `plotly`

4. For `sc/`:
   - Ensure `*_output.tsv` (InterProScan output) is present

---

## ⚠️ Notes

- `3D.RData` is **fully reproducible** from `Rain.R`
- Azimuth-based annotation is **optional** and disabled by default
- Domain filtering removes zero-variance features and cells
- Visualization uses **Plotly 3D scatter (point-based towers)**