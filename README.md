# ReSMOTE-DR

ReSMOTE-DR is a diabetic retinopathy image-analysis experiment built around EfficientNet-B4, multi-task regression/classification training, feature-level SMOTE, threshold search and multi-branch ensemble inference.

The repository preserves the training, classification-head, threshold-search and inference notebooks together with lightweight result records. Raw retinal image datasets, local CSV splits, preprocessing caches and model checkpoints are intentionally excluded.

## Technical Highlights

- **EfficientNet-B4 backbone**: uses an ImageNet-pretrained EfficientNet-B4 as the retinal image feature extractor.
- **Multi-task output head**: trains one regression output for continuous severity prediction and five classification logits for DR grade prediction.
- **Feature-level SMOTE**: extracts EfficientNet-B4 features, balances them in feature space and trains a residual MLP classification head.
- **Threshold search**: optimizes regression-output thresholds for converting continuous predictions into discrete severity grades.
- **Branch comparison**: evaluates regression, backbone classification, residual MLP classification and ensemble inference paths.

## Repository Layout

```text
ReSMOTE-DR/
├── notebooks/              # Main training, head-training and inference notebooks
├── results/                # Lightweight result markers, threshold files and copied experiment notebooks
├── docs/                   # Method and reproducibility notes
├── requirements.txt        # Python dependencies used by the notebooks
├── .gitignore              # Excludes datasets, caches and model weights
└── README.md
```

## Method Overview

The experiment workflow is:

1. Load APTOS-style retinal fundus images and resize them to `448 x 448`.
2. Train an EfficientNet-B4 backbone with a multi-task head:
   - regression output for continuous severity prediction,
   - five classification logits for severity-class prediction.
3. Optimize thresholds for mapping regression outputs to integer DR grades.
4. Extract EfficientNet-B4 features and apply feature-level SMOTE.
5. Train a residual MLP classification head on the balanced feature vectors.
6. Compare regression, backbone-head, MLP-head and ensemble predictions with accuracy and quadratic kappa.

## Notebook Result Summary

Representative metrics below are taken from preserved notebook outputs. They are experiment records, not benchmark claims, because the public repository does not include the original data splits or checkpoints required for exact reproduction.

| Experiment branch / strategy | Preserved notebook output |
| --- | --- |
| EfficientNet-B4 multi-task backbone | Best validation loss marker: `0.6841`; validation classification accuracy reached `0.8306`; regression kappa reached `0.8859` in logged epochs |
| Residual MLP head with EfficientNet-B4 features + SMOTE | Validation accuracy reached `0.8279` in the main notebook; copied experiment records include `best_val_acc0.8251.txt` and `best_val_acc0.8279.txt` |
| Regression branch with optimized thresholds | Accuracy around `0.7992-0.8101`; quadratic kappa around `0.8869-0.8917` in inference notebooks |
| Backbone classification branch | Accuracy around `0.8265-0.8292`; quadratic kappa around `0.8791-0.8970` |
| Residual MLP classification branch | Accuracy up to `0.8484`; quadratic kappa up to `0.9017` |
| Multi-branch ensemble | Accuracy around `0.8279-0.8388`; quadratic kappa up to `0.9040` |

The results show why the experiment compares multiple prediction paths rather than relying on a single head: the MLP branch gives the highest recorded accuracy, while static ensemble inference records the highest quadratic kappa.

## Notebooks

| Notebook | Purpose |
| --- | --- |
| `notebooks/EfficientNet-B4_multi_task.ipynb` | EfficientNet-B4 regression + classification training |
| `notebooks/classify_head_effb4.ipynb` | Feature extraction, SMOTE balancing and residual MLP head training |
| `notebooks/dynamic_weight_ensemble.ipynb` | Dynamic class-wise ensemble inference |
| `notebooks/static_weight_ensemble.ipynb` | Static ensemble inference variants |
| `notebooks/test_head_inference.ipynb` | Test/inference workflow and branch comparison |

Copied notebooks under `results/` preserve the experiment variants associated with specific result markers.

## Preserved Result Files

| Record | Meaning |
| --- | --- |
| `results/exp_effb4_regression/exp2/best_val_loss_0.6841.txt` | Best validation-loss marker from an EfficientNet-B4 multi-task run |
| `results/exp_effb4_regression/exp2/classify_head/exp2/best_val_acc0.8251.txt` | Classification-head validation accuracy marker |
| `results/exp_effb4_regression/exp4-no-early/classify_head/exp1/best_val_acc0.8279.txt` | Classification-head validation accuracy marker from a no-early-stop run |
| `best_grid_thresholds.pkl` files | Optimized threshold arrays for regression-output discretization |

## Data and Checkpoint Policy

This repository intentionally excludes:

- raw retinal image datasets,
- local train/validation/test CSV splits,
- cached `*.npy` image arrays,
- PyTorch model checkpoints such as `*.pth`,
- large generated outputs.

Expected local data layout for rerunning the notebooks is APTOS-style:

```text
data/
├── aptos/
│   ├── image/
│   └── train.csv
└── aptos-2019/
    ├── val_images/
    ├── test_images/
    ├── valid.csv
    └── test.csv
```

## Environment

Install the core dependencies with:

```bash
pip install -r requirements.txt
```

The experiments were designed for a CUDA-enabled PyTorch environment. Some notebook paths and GPU IDs reflect the original training machine and should be adjusted before rerunning.

## Technical Scope

This repository covers:

- EfficientNet-B4 transfer learning for retinal image grading,
- multi-task regression/classification training,
- feature-level class balancing with SMOTE,
- residual MLP classification-head experiments,
- threshold search for regression-output discretization,
- branch-level and ensemble metric comparison,
- public-release boundaries for medical-image experiments.
