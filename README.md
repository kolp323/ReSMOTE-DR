# ReSMOTE-DR

ReSMOTE-DR is a diabetic retinopathy image-analysis experiment built around EfficientNet-B4, multi-task regression/classification training, feature-level SMOTE, threshold search and multi-branch ensemble inference.

The workflow covers backbone training, feature extraction, SMOTE-balanced head training, regression-threshold optimization and ensemble evaluation for DR severity grading.

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
├── results/                # Result files, threshold files and experiment notebooks
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

The table below summarizes representative metrics from the experiment notebooks.

| Experiment branch / strategy | Notebook output |
| --- | --- |
| EfficientNet-B4 multi-task backbone | Best validation loss: `0.6841`; validation classification accuracy reached `0.8306`; regression kappa reached `0.8859` in logged epochs |
| Residual MLP head with EfficientNet-B4 features + SMOTE | Validation accuracy reached `0.8279`; result files include `best_val_acc0.8251.txt` and `best_val_acc0.8279.txt` |
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

Experiment notebooks under `results/` document the variants associated with specific result files.

## Result Files

| Record | Meaning |
| --- | --- |
| `results/exp_effb4_regression/exp2/best_val_loss_0.6841.txt` | Best validation loss from an EfficientNet-B4 multi-task run |
| `results/exp_effb4_regression/exp2/classify_head/exp2/best_val_acc0.8251.txt` | Classification-head validation accuracy |
| `results/exp_effb4_regression/exp4-no-early/classify_head/exp1/best_val_acc0.8279.txt` | Classification-head validation accuracy from a no-early-stop run |
| `best_grid_thresholds.pkl` files | Optimized threshold arrays for regression-output discretization |

## Data Layout

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

The experiments were designed for a CUDA-enabled PyTorch environment. Update notebook paths and GPU IDs as needed for your local setup.

## Technical Scope

This repository covers:

- EfficientNet-B4 transfer learning for retinal image grading,
- multi-task regression/classification training,
- feature-level class balancing with SMOTE,
- residual MLP classification-head experiments,
- threshold search for regression-output discretization,
- branch-level and ensemble metric comparison,
- reproducibility notes for local medical-image experiments.
