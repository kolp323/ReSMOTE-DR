# ReSMOTE-DR

ReSMOTE-DR is a portfolio-oriented diabetic retinopathy image-analysis project built around EfficientNet-B4, multi-task learning, feature-level SMOTE, threshold search and multi-branch ensemble inference.

本项目由 `d2l/avote_all_train` 中的医学图像实验整理而来，保留了具有展示价值的训练、分类头、阈值搜索和集成推理实验记录。公开仓库不包含原始医学图像数据、预处理缓存或模型权重。

## Project Highlights

- **EfficientNet-B4 backbone**: uses an ImageNet-pretrained EfficientNet-B4 as the main retinal image feature extractor.
- **Multi-task learning**: trains a shared backbone with one regression output and five classification logits for diabetic retinopathy severity prediction.
- **Feature-level SMOTE**: extracts EfficientNet features and applies SMOTE in feature space to handle class imbalance before training a residual MLP classification head.
- **Threshold search**: preserves optimized threshold files for mapping continuous regression predictions to discrete DR severity classes.
- **Multi-branch ensemble**: compares regression, backbone classification, MLP classification and ensemble strategies with validation/test metrics.

## Repository Layout

```text
ReSMOTE-DR/
├── notebooks/              # Training, classification-head and inference notebooks
├── results/                # Lightweight result markers and threshold files
├── docs/                   # Architecture and packaging notes
├── requirements.txt        # Python dependencies used by the notebooks
├── .gitignore              # Excludes datasets, caches and model weights
└── README.md
```

## Method Overview

The experiment workflow is:

1. Load APTOS-style retinal fundus images and resize them to `448 x 448`.
2. Train an EfficientNet-B4 multi-task model with:
   - one regression output for continuous severity prediction,
   - five classification logits for severity-class prediction.
3. Optimize thresholds for converting regression outputs into integer DR grades.
4. Extract backbone features and train a residual MLP classification head after feature-level SMOTE balancing.
5. Evaluate regression, backbone-head, MLP-head and ensemble predictions.

## Notebooks

| Notebook | Purpose |
| --- | --- |
| `notebooks/EfficientNet-B4_multi_task.ipynb` | EfficientNet-B4 regression + classification training |
| `notebooks/classify_head_effb4.ipynb` | Feature extraction, SMOTE balancing and residual MLP head training |
| `notebooks/dynamic_weight_ensemble.ipynb` | Dynamic class-wise ensemble inference |
| `notebooks/static_weight_ensemble.ipynb` | Static ensemble inference variants |
| `notebooks/test_head_inference.ipynb` | Test/inference workflow and branch comparison |

## Key Experiment Records

The repository keeps lightweight result markers under `results/`:

| Record | Meaning |
| --- | --- |
| `best_val_loss_0.6841.txt` | Best validation-loss marker from an EfficientNet-B4 multi-task run |
| `best_val_acc0.8251.txt` | Classification-head validation accuracy marker |
| `best_val_acc0.8279.txt` | Best classification-head validation accuracy marker in a no-early-stop run |
| `best_grid_thresholds.pkl` | Optimized threshold arrays for regression-output discretization |

Representative notebook outputs include:

| Branch / strategy | Example metric from preserved outputs |
| --- | --- |
| Regression branch with optimized thresholds | Acc around `0.80`, quadratic kappa around `0.89` |
| Backbone classification head | Acc around `0.83`, quadratic kappa around `0.88-0.90` |
| Residual MLP classification head | Acc up to `0.8484`, quadratic kappa up to `0.9017` |
| Multi-branch ensemble | Acc around `0.83-0.84`, quadratic kappa up to `0.9040` |

These values are preserved as experiment evidence in the notebooks rather than as a formal benchmark claim, because the public repository does not include the original data splits or model checkpoints.

## Data and Checkpoint Policy

This public repository intentionally excludes:

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

## Project Value

This project demonstrates:

- medical image classification experiment design,
- EfficientNet-B4 transfer learning,
- multi-task regression/classification training,
- class-imbalance handling with SMOTE,
- validation-threshold search,
- ensemble inference and metric analysis,
- packaging a research-style notebook experiment into a public portfolio project.
