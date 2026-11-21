# BioAI-X

## Day 1: Dataset Preparation (`GSE76275`)
- **Dataset:** `GSE76275` (*Homo sapiens* breast cancer microarray data, platform GPL570)
- **Samples:** 265 total (198 TNBC = `1`, 67 non-TNBC = `0`)
- **Notebook:** `day1_tnbc_data.ipynb`

## Day 2: Probe Mapping, DEG Analysis & Visualizations (`GSE76275`)
- **Notebook:** `day2_deg_analysis.ipynb`
- **Probe-to-Gene Mapping:** Mapped GPL570 probe IDs to **21,655** gene symbols across **265** samples (max expression ~15.62, no log2 conversion needed).
- **Differential Expression (DEGs):** Ran Welch's t-test (`TNBC` vs. `non-TNBC`) with Benjamini-Hochberg FDR correction, identifying **13,496** significant genes (`FDR < 0.05`).
- **Outputs:** `top500_DEGs.csv`, `tnbc_top500.csv`, `pca.png`, and `volcano.png`.

Day 2: DEG analysis and plots done.
