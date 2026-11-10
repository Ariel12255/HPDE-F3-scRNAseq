# HPDE scRNA-seq: heterogeneity and F3 (CD142) analysis

Single-cell RNA-seq analysis of the HPDE human pancreatic ductal epithelial cell line.

**Data:** GEO [GSE173339](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE173339), sample GSM5266069 (HPDE), 10x Genomics, Cell Ranger 3.0.1 filtered matrix.
From: Monberg et al., *Occult polyclonality of preclinical pancreatic cancer models drives in vitro evolution*, Nat Commun 2022.

## Questions
1. **Heterogeneity:** do HPDE cells form distinct transcriptional clusters, and which genes mark each cluster?
2. **F3 (CD142):** what fraction of cells express F3 RNA, and how is it distributed?
3. **Co-expression:** which genes are co-expressed in F3-high cells?
4. **Surface markers:** which cell-surface proteins could be used with antibodies to isolate the F3-enriched population?

## Key results
- 5,233 cells → **5,185** after QC; Scrublet doublets 2.8% (spread across clusters, not removed).
- **7 clusters** (Leiden, resolution 0.5): stress response, CTNNB1+/B4GALT1+, F3-high/PAQR7+, AREG/CAV1 epithelial, mitotic, ribosomal/S100A6-high, cycling G2/M.
- **F3 RNA detected in 68.0% of cells** (3,528/5,185); highest in clusters 1 (85%) and 2 (88%), which together form the **F3-enriched population (1,641 cells, 32%)**.
- F3-high vs F3-zero cells: 270 genes up, 175 down; top co-expressed genes include CDC42, SET, PAQR7, EIF5A, YWHAE, PPP1CB, CTNNB1, AREG, AXL, CCND1.
- Best antibody-accessible surface marker for the F3-enriched population: **F3 / CD142** (86% vs 60%), with **ADAM17 / CD156b** (58% vs 34%) as a second marker. PAQR7 separates best at RNA level (87% vs 33%) but has little extracellular sequence.

## Figures (`figures/`)
| File | Content |
|---|---|
| `violin_1_QC.png` | QC: genes, UMI and mitochondrial % per cell |
| `pca_variance_ratio_2_PCA_variance.png` | PCA variance explained |
| `umap_3_leiden_resolutions.png` | Clusters at Leiden resolutions 0.2 / 0.3 / 0.5 / 0.8 |
| `umap_4_clusters_qc.png` | Clusters, cell-cycle phase, UMI, mito %, doublet score |
| `Q1_A_7clusters_UMAP.png` | **Q1** – 7 named clusters |
| `Q1_B_F3_enriched_UMAP.png` | **Q1** – F3-enriched clusters (1 + 2) highlighted, F3+ % per cluster |
| `dotplot__5_markers.png` | **Q1** – top 5 marker genes per cluster |
| `umap_6_F3.png` | **Q2** – F3 expression on UMAP |
| `violin_7_F3_by_cluster.png` | **Q2** – F3 expression per cluster |
| `dotplot__8_F3_coexpr.png` | **Q3** – top 30 genes co-expressed with F3 (F3-zero / mid / high) |
| `umap_9_F3_coexpr.png` | **Q3** – F3 and top co-expressed genes on UMAP |
| `dotplot__Surface_1.png` | **Q4** – top surface candidates across clusters |
| `umap_Surface_2.png` | **Q4** – F3 and top surface candidates on UMAP |
| `Surface_3_pct_in_vs_out.png` | **Q4** – % expressing in F3-enriched vs other cells |

## Tables (`results/`)
- `HPDE_Scanpy_Results_Summary.xlsx` – key numbers, cluster summary, markers, F3 co-expression, per-cell table, methods
- `F3_enriched_surface_markers.xlsx` – surface-protein candidates (UniProt + CSPA + HPA annotation)

## Code (`scripts/`)
- `run_scanpy.py` – full pipeline (QC → clustering → markers → F3 analysis)
- `plot_q1_clusters.py` – Q1 figures
- `surface_markers.py` – surface-marker analysis for the F3-enriched clusters

Reproduce: download `GSM5266069_HPDE_filtered_feature_bc_matrix.tar.gz` from GEO into the parent folder of the scripts, then
```
pip install scanpy leidenalg openpyxl
python run_scanpy.py
python plot_q1_clusters.py
python surface_markers.py
```

## Methods (short)
QC: median ± 5 MAD on log UMI and genes, mito ≤ 10%; genes in ≥ 3 cells. Normalise to 1e4 + log1p; 2,000 HVGs; PCA (30 PCs), kNN (k = 15), Leiden, UMAP. Cell cycle: Tirosh 2016 gene sets. Markers: Wilcoxon, padj < 0.05, log2FC > 0.5, ≥ 25% of cells. F3-high = top 25% of cells by F3; compared with F3-zero cells, validated by Spearman correlation. Surface proteins: UniProt (cell membrane + transmembrane/GPI-anchored) or CSPA.

**Caveat:** all results are RNA-level; CD142 protein expression should be confirmed by flow cytometry.
