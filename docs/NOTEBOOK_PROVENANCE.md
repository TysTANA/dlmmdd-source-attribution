# Notebook provenance

This document records the relationship between the original experiment
notebooks and the cleaned notebooks published in this repository.

The original notebooks are archived outside the Git repository and are
kept unchanged. Their SHA-256 hashes are recorded below so that the exact
source files used for the public refactoring can be identified later.

The public notebooks were reorganized after the challenge to improve
readability, portability, and reproducibility. The refactoring removed
local absolute paths, duplicate or inactive cells, large embedded outputs,
and unrelated experimental branches. It was intended to preserve the
model definitions, fold assignments, training settings, checkpoint
selection rules, inference rules, and evaluation procedures relevant to
the paper.

## Official challenge reproduction

The official challenge pipeline is provided in:

- `dlmmdd_solution_reproduction.ipynb`

The official competition result used the CE+ArcFace 60:40 base branch and
the submitted ensemble. The CE-only, ArcFace-only, and CE+ArcFace 20:80
checkpoints described below were trained after the challenge for controlled
diagnostic analyses and did not affect the official sixth-place ranking.

## Public notebook mapping

| Public notebook | Archived original notebook | Purpose and main refactoring |
|---|---|---|
| `diagnostics/training/train_ce_only_arcface_only.ipynb` | `arcface_base_ce_only.ipynb` | Combines the CE-only and ArcFace-only diagnostic training paths using shared code; removes duplicate executions, inactive branches, local comparison cells, absolute paths, and embedded outputs while retaining the patience-4 training configuration and original checkpoint prefixes. |
| `diagnostics/training/train_ce20arc80_paper.ipynb` | `arcface_base_ce20arc80.ipynb` | Preserves the CE 20% + ArcFace 80% training objective and the 50:50 CE/ArcFace logit fusion used for checkpoint selection and inference; removes the local comparison cell, absolute paths, and unrelated branches. |
| `diagnostics/training/ensemble_arcface_traingrid_norm_sweep.ipynb` | Existing diagnostic notebook | Moved under `diagnostics/training/` to separate diagnostic training from the official challenge reproduction. |
| `diagnostics/analysis/representation_analysis.ipynb` | `tsne_patience4_representation_analysis-pdf.ipynb` | Preserves the fold-wise 512-dimensional representation metrics, supplementary t-SNE visualization, orthogonal Procrustes alignment, and shared-PCA Figure 2 workflow; removes duplicate plotting cells and local absolute paths. |
| `diagnostics/analysis/postprocessing_robustness.ipynb` | `Controlled_post-processing_OOF_robustness_evaluation2.ipynb` | Preserves deterministic five-fold OOF evaluation with TTA disabled for clean, JPEG70, blur12, down50, and crop80 conditions; consolidates the four model objectives and removes unused transformations and duplicate evaluation cells. |

Use the exact archived filenames in the table above if the locally archived
copies include suffixes such as `(1)`, `(2)`, or `(3)`.

## Original notebook SHA-256 hashes

Replace each placeholder below with the SHA-256 value saved before
refactoring.

| Archived original notebook | SHA-256 |
|---|---|
| `arcface_base_ce_only.ipynb` | `C1EB7DF4CAA8FB56EAEBC3A4EECB821A8526A10EA6E3CEEED3F35A061219061A` |
| `arcface_base_ce20arc80.ipynb` | `7C3088C529F878B476F02D078D5888742FD1F8F85E671A8C6BEDA9A9D50FDAC2` |
| `Controlled_post-processing_OOF_robustness_evaluation2.ipynb` | `B656DB6341CF3E2EB22EDD5DF257DBC253657E33C140FA3ED3730EA4167045A1` |
| `tsne_patience4_representation_analysis-pdf.ipynb` | `5CD16102C33989DEED7E8830C68A8F6DA61BA66E78518B241CCB13FE9BA8BF09` |

## Checkpoint provenance

The analysis notebooks do not retrain models. They load the existing
patience-4 checkpoints used for the paper-facing diagnostics.

### Official CE+ArcFace 60:40 checkpoints

```text
ensemble_artifacts/
├─ arcface_base_best_fold0.pth
├─ arcface_base_best_fold1.pth
├─ arcface_base_best_fold2.pth
├─ arcface_base_best_fold3.pth
└─ arcface_base_best_fold4.pth
```

### Post-challenge diagnostic checkpoints

```text
ensemble_artifacts/diagnostics/
├─ arcface_base_ce_only_best_fold0.pth
├─ ...
├─ arcface_base_ce_only_best_fold4.pth
├─ arcface_base_arcface_only_best_fold0.pth
├─ ...
├─ arcface_base_arcface_only_best_fold4.pth
├─ arcface_base_ce20arc80_best_fold0.pth
├─ ...
└─ arcface_base_ce20arc80_best_fold4.pth
```

The checkpoint files are excluded from Git because of their size. The
training notebooks document how the diagnostic checkpoints can be
regenerated.

## Paper-result correspondence

| Paper item | Public notebook | Generated artifact |
|---|---|---|
| Table 1: representation diagnostics | `diagnostics/analysis/representation_analysis.ipynb` | `results/diagnostics/table1_representation_metrics.csv` |
| Figure 2: Procrustes-aligned shared PCA | `diagnostics/analysis/representation_analysis.ipynb` | `results/diagnostics/figure2_shared_pca.pdf` and `.png` |
| Supplementary t-SNE visualization | `diagnostics/analysis/representation_analysis.ipynb` | `results/diagnostics/supplementary_tsne_fold0.pdf` and `.png` |
| Table 2: controlled post-processing robustness | `diagnostics/analysis/postprocessing_robustness.ipynb` | `results/diagnostics/table2_postprocessing_accuracy.csv` |
| Detailed robustness diagnostics | `diagnostics/analysis/postprocessing_robustness.ipynb` | `postprocessing_summary.csv`, `postprocessing_fold_results.csv`, `postprocessing_classwise_accuracy.csv`, and `postprocessing_metadata.json` |

## Verification after refactoring

After the public notebooks were reorganized, the analysis notebooks were
executed from top to bottom using the archived checkpoints.

The generated results were checked against the camera-ready paper:

- Table 1 matched the reported values at the displayed precision.
- Table 2 matched the reported values at the displayed precision.
- Figure 2 reproduced the four-panel Procrustes-aligned shared-PCA
  visualization using CE+ArcFace 60:40 as the reference representation.
- Representation metrics were computed in the original normalized
  512-dimensional embedding space, not in the two-dimensional PCA space.
- The post-processing evaluation used five-fold OOF inference, no
  retraining, no TTA, and no mixed-precision inference.

## Reproducibility boundary

The repository does not redistribute the competition dataset or model
checkpoints. Exact numerical reproduction therefore requires access to
the original dataset and the archived checkpoints. Re-training from the
published notebooks may produce small numerical differences because of
GPU hardware, PyTorch, CUDA, cuDNN, image-decoding, or dependency-version
differences.
