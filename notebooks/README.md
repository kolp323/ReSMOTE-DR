# Notebooks

| Notebook | Purpose |
| --- | --- |
| `EfficientNet-B4_multi_task.ipynb` | Train an EfficientNet-B4 backbone with regression and classification outputs. |
| `classify_head_effb4.ipynb` | Extract EfficientNet-B4 features, apply feature-level SMOTE and train a residual MLP head. |
| `dynamic_weight_ensemble.ipynb` | Evaluate dynamic class-wise ensemble weighting across regression, backbone and MLP branches. |
| `static_weight_ensemble.ipynb` | Evaluate fixed-weight ensemble variants. |
| `test_head_inference.ipynb` | Run inference and compare branch-level metrics. |

The notebooks expect an APTOS-style dataset directory outside the repository. The public repository does not include raw medical images or model checkpoints.
