# Results

This directory keeps lightweight experiment records from the ReSMOTE-DR workflow.

## Preserved Records

- `best_val_loss_0.6841.txt`: best validation-loss marker from a multi-task EfficientNet-B4 run.
- `best_val_acc0.8251.txt`: classification-head validation accuracy marker.
- `best_val_acc0.8279.txt`: classification-head validation accuracy marker from the no-early-stop run.
- `best_grid_thresholds.pkl`: optimized thresholds used to discretize regression outputs into DR severity classes.
- Copied notebooks inside each experiment folder preserve the experiment variant used for that run.

## Notebook Metric Summary

| Branch / strategy | Accuracy | Quadratic kappa |
| --- | --- | --- |
| Regression branch with optimized thresholds | `0.7992-0.8101` | `0.8869-0.8917` |
| Backbone classification branch | `0.8265-0.8292` | `0.8791-0.8970` |
| Residual MLP classification branch | up to `0.8484` | up to `0.9017` |
| Multi-branch ensemble | `0.8279-0.8388` | up to `0.9040` |

These numbers are taken from preserved notebook outputs. Exact reproduction requires the original data splits and checkpoints, which are not included in this repository.

## Excluded Records

Raw medical images, CSV splits, cached NumPy arrays and PyTorch checkpoints are intentionally excluded.
