# Architecture Notes

## Project Story

ReSMOTE-DR is a diabetic retinopathy image-analysis experiment built around EfficientNet-B4, multi-task learning, feature-level SMOTE, classification-head training and ensemble inference.

The repository is packaged as a public portfolio project. It keeps the experiment notebooks and lightweight result markers, while excluding raw medical images, cached NumPy arrays and model checkpoints.

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

The lightweight files under `results/` preserve the best validation markers from the experiments:

| Result marker | Meaning |
| --- | --- |
| `best_val_loss_0.6841.txt` | Best recorded validation loss marker for an EfficientNet-B4 multi-task run |
| `best_val_acc0.8251.txt` | Best recorded classification-head validation accuracy marker |
| `best_val_acc0.8279.txt` | Best recorded classification-head validation accuracy marker from the no-early-stop run |
| `best_grid_thresholds.pkl` | Saved optimized threshold arrays for regression-output discretization |

Additional notebook outputs record inference comparisons including regression, backbone-head, MLP-head and ensemble metrics.

## Public Packaging Decisions

Excluded content:

- raw retinal image datasets,
- local CSV splits and image caches,
- `*.npy` preprocessing caches,
- PyTorch model checkpoints such as `*.pth`,
- large or environment-specific generated outputs.

Preserved content:

- experiment notebooks,
- result marker files,
- optimized threshold pickle files,
- documentation describing the method and repository scope.
