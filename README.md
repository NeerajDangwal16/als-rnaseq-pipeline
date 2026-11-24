# ALS Blood RNA-seq Biomarker Pipeline

A reproducible analysis of whole-blood RNA-seq from sporadic ALS patients and controls, aimed at finding blood-based biomarkers and, just as importantly, testing how far they can be trusted.

The project started as a standard discovery pipeline (differential expression, WGCNA, machine learning) and was then extended with a set of stress tests: covariate adjustment, nested cross-validation, external validation, cell-composition checks and specificity checks against other diseases. The main finding is a modest but replicable ALS blood signal that is partly an immune-cell composition effect, with one gene, VNN3P, holding up after adjustment.

## Data

| Accession | Description | Role |
|---|---|---|
| [GSE234297](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE234297) | Blood RNA-seq, 96 sporadic ALS vs 48 controls | Discovery |
| [GSE112680](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE112680) | Whole-blood array (GPL10558), 164 ALS, 137 controls, 75 ALS-mimic patients | External validation, replication, mimic comparison |
| [GSE63061](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE63061) | Whole-blood array (GPL10558), 139 Alzheimer's vs 134 controls | Specificity check |

A published 16-gene blood signature (Shvetcov et al., 2023, *Int J Mol Sci* 24:15011) was used as a literature cross-check.

## Pipeline

1. **Differential expression** with PyDESeq2 on GSE234297.
2. **Co-expression network** with WGCNA; the ME4 module tracked ALS status.
3. **Overlap** of DEGs and ME4 genes (hypergeometric test, 5,000-gene background).
4. **Feature selection and classification**: LASSO followed by Random Forest, giving a six-gene signature: *P2RY13, IGF1R, KCNJ15, IRF2, IL27RA, VNN3P*.
5. **Re-analysis with adjustment** for sex, age group and flowcell.
6. **Validation**: nested cross-validation, frozen-model external validation, DEG replication.
7. **Biology and specificity**: cell-composition scores, g:Profiler enrichment, Alzheimer's blood, ALS-mimic patients, comparison with the published signature.

## Results

| Question | Result |
|---|---|
| Does the signal survive adjustment for sex, age group and flowcell? | Yes: 324 DEGs (310 in the matched unadjusted run); all six genes stay significant (padj 0.016 to 0.043) |
| Is the original accuracy meaningful? | No. 67.4% cross-validated accuracy equals the 66.7% majority-class baseline (96 of 144 samples are ALS) |
| Honest in-cohort performance | Nested CV AUC 0.57 to 0.58 |
| External validation of the frozen six-gene model (GSE112680) | AUC 0.66, 95% CI 0.60 to 0.72; dropping any one gene gives 0.62 to 0.67 |
| Does the full DEG list replicate? | 71% of 283 measurable DEGs move in the same direction (p = 1e-12); Spearman 0.52; 79 genes significant externally |
| Is it a cell-composition effect? | Largely. Neutrophil marker score is higher in ALS in both cohorts; external DEGs fall from 88 to 65 after adjustment |
| Which of the six genes hold up? | Only VNN3P stays clearly significant; IRF2 reverses direction |
| Shared with Alzheimer's blood? | Mostly not: 46% same direction vs 71% in ALS |
| Separates ALS from look-alike conditions? | Yes, at the gene-list level: 73% same direction for ALS vs mimics, 51% for mimics vs controls |
| Published 16-gene signature | 9 of 16 genes measurable; 8 of 9 agree in direction; cross-cohort AUC 0.63 and 0.74 (their within-cohort AUC of 0.89 is not reproduced) |

## Limitations

- Cell composition is estimated from a few marker genes (neutrophil: FCGR3B, CSF3R, CXCR2, S100A8, S100A9; T cell: CD3E, CD3D, CD2, IL7R). These scores are crude and may over-adjust. A formal deconvolution is still to do.
- Effects are modest; adjusted p-values for the six genes sit just under 0.05.
- WGCNA was run on non-log counts, so the ME4 module should be treated as exploratory.
- External validation covers a single array cohort, without age or composition adjustment.
- Specificity was tested against one Alzheimer's cohort, and the mimic group is a mix of diagnoses; the mimic gene-level tests are not composition-adjusted.
- VNN3P is annotated as a pseudogene; array probes are labelled VNN3, so its function is unclear.

## Reproducing the analysis

The analysis was run in Python (Google Colab). Pinned versions that work together:

```bash
pip install "pandas==2.3.3" "pydeseq2==0.4.12" "anndata==0.10.9"
# plus: numpy, scipy, scikit-learn, matplotlib, statsmodels, GEOparse, mygene, gprofiler-official
```

Restart the runtime after installing. Notes that will save you time:

- PyDESeq2 treats every design column as categorical, so numeric age must be binned (<50, 50s, 60s, 70s, 80+) before it goes into the design.
- The adjusted design is `["sex", "agebin", "flowcell", "condition"]`.
- Expression from GSE112680 and GSE63061 is z-scored within each cohort, and genes are matched across platforms by Entrez ID.
- Colab deletes files when the session resets, so download your results.

## Repository layout

Adjust this to match what is actually in the repo.

```
.
├── README.md
├── notebooks/        # Colab / Jupyter notebooks
├── results/          # DEG tables, figures (ROC, replication scatter)
└── docs/             # progress slides
```

## Next steps

- Formal cell-type deconvolution, then re-test VNN3P and the DEG list
- Survival and site-of-onset models in GSE112680
- More specificity checks: Parkinson's blood data, mimic group split by diagnosis
- Single-cell data (GSE244263) to see which cell type carries the signal
- VNN3P follow-up: probe annotation, expression by cell type, neutrophil biology

## References

- Shvetcov A. et al. (2023). *Int J Mol Sci* 24:15011.
- Muzellec B. et al. PyDESeq2: a Python package for bulk RNA-seq differential expression analysis.
- Langfelder P., Horvath S. (2008). WGCNA: an R package for weighted correlation network analysis. *BMC Bioinformatics* 9:559.

## Author

Neeraj Dangwal, M.Tech Bioinformatics, IIIT Allahabad. Supervisor: Dr. Sangeeta Singh.
