# Results

This directory keeps lightweight experiment records from the ReSMOTE-DR workflow.

## Preserved records

- `best_val_loss_0.6841.txt`: best validation-loss marker from a multi-task EfficientNet-B4 run.
- `best_val_acc0.8251.txt`: classification-head validation accuracy marker.
- `best_val_acc0.8279.txt`: classification-head validation accuracy marker from the no-early-stop run.
- `best_grid_thresholds.pkl`: optimized thresholds used to discretize regression outputs into DR severity classes.
- Copied notebooks inside each experiment folder preserve the exact experiment variant used for that run.

## Excluded records

Raw medical images, CSV splits, cached NumPy arrays and PyTorch checkpoints are intentionally excluded from the public repository.
