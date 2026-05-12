# Architecture Notes

## Project Story

ReSMOTE-DR is a diabetic retinopathy image-analysis experiment built around EfficientNet-B4, multi-task learning, feature-level SMOTE, classification-head training and ensemble inference.

The repository keeps the experiment notebooks and lightweight result records, while excluding raw medical images, cached NumPy arrays and model checkpoints.

## Workflow

1. Load APTOS-style retinal fundus image metadata and resize images to 448 x 448.
2. Train an EfficientNet-B4 backbone with a multi-task output head for regression and classification.
3. Save regression validation outputs and optimize thresholds for converting continuous predictions into DR severity classes.
4. Extract EfficientNet-B4 features and train a residual MLP classification head with feature-level SMOTE.
5. Compare regression, backbone classification, MLP classification and ensemble inference strategies.

## Main Components

| Area | Notebook | Purpose |
| --- | --- | --- |
| Multi-task backbone | `notebooks/EfficientNet-B4_multi_task.ipynb` | EfficientNet-B4 regression + classification training |
| Classification head | `notebooks/classify_head_effb4.ipynb` | Feature extraction, SMOTE balancing and residual MLP head training |
| Dynamic ensemble | `notebooks/dynamic_weight_ensemble.ipynb` | Multi-branch inference with dynamic class-wise weighting |
| Static ensemble | `notebooks/static_weight_ensemble.ipynb` | Multi-branch inference with fixed fusion weights |
| Test/inference head | `notebooks/test_head_inference.ipynb` | Inference and evaluation workflow for trained heads |

## Metrics Preserved in This Repository

The lightweight files under `results/` preserve validation markers and copied experiment notebooks:

| Result marker | Meaning |
| --- | --- |
| `best_val_loss_0.6841.txt` | Best recorded validation loss marker for an EfficientNet-B4 multi-task run |
| `best_val_acc0.8251.txt` | Classification-head validation accuracy marker |
| `best_val_acc0.8279.txt` | Classification-head validation accuracy marker from a no-early-stop run |
| `best_grid_thresholds.pkl` | Saved optimized threshold arrays for regression-output discretization |

Representative notebook outputs include:

| Branch / strategy | Accuracy | Quadratic kappa |
| --- | --- | --- |
| Regression branch with optimized thresholds | `0.7992-0.8101` | `0.8869-0.8917` |
| Backbone classification branch | `0.8265-0.8292` | `0.8791-0.8970` |
| Residual MLP classification branch | up to `0.8484` | up to `0.9017` |
| Multi-branch ensemble | `0.8279-0.8388` | up to `0.9040` |

## Public Release Boundaries

Excluded content:

- raw retinal image datasets,
- local CSV splits and image caches,
- `*.npy` preprocessing caches,
- PyTorch model checkpoints such as `*.pth`,
- large or environment-specific generated outputs.

Preserved content:

- experiment notebooks,
- copied notebooks for recorded experiment variants,
- result marker files,
- optimized threshold pickle files,
- documentation describing the method and reproducibility boundaries.
