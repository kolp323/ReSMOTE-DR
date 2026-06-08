# ReSMOTE-DR

[中文版](#中文版) | [English Version](#english-version)

---

<a name="中文版"></a>
# ReSMOTE-DR (中文版)

ReSMOTE-DR 是一个专注于糖尿病视网膜病变（Diabetic Retinopathy, DR）图像分析的实验性项目。该项目基于 EfficientNet-B4 模型，结合多任务回归/分类训练、特征级 SMOTE（合成少数类过采样技术）、阈值搜索和多分支集成推理技术。

工作流程涵盖了骨干网络训练、特征提取、基于 SMOTE 平衡数据的头部训练、回归阈值优化以及针对 DR 严重程度分级的集成评估。

## 技术亮点

- **EfficientNet-B4 骨干网络**：使用在 ImageNet 上预训练的 EfficientNet-B4 作为视网膜图像特征提取器。
- **多任务输出头**：训练一个回归输出用于连续严重程度预测，以及五个分类 logit 用于 DR 等级预测。
- **特征级 SMOTE**：提取 EfficientNet-B4 特征，在特征空间进行样本平衡，并训练一个残差 MLP 分类头。
- **阈值搜索**：优化回归输出的阈值，将连续预测转换为离散的严重程度等级。
- **多分支对比**：评估和比较回归分支、骨干分类分支、残差 MLP 分类分支以及集成推理路径的性能。

## 目录结构

```text
ReSMOTE-DR/
├── notebooks/              # 主要的训练、头部训练和推理 Jupyter Notebooks
├── results/                # 结果文件、阈值文件和实验 Notebooks
├── docs/                   # 方法和复现说明文档
├── requirements.txt        # 项目依赖
├── LICENSE                 # 开源许可证
└── README.md               # 项目说明文档
```

## 方法概述

实验工作流程如下：

1. 加载 APTOS 风格的视网膜眼底图像，并将其调整为 `448 x 448` 像素。
2. 训练带有多任务头的 EfficientNet-B4 骨干网络：
   - 回归输出用于连续的严重程度预测。
   - 五个分类 logits 用于预测严重程度类别。
3. 优化阈值，以便将回归输出映射到离散的 DR 等级（整数）。
4. 提取 EfficientNet-B4 特征，并应用特征级 SMOTE 进行样本平衡。
5. 在平衡后的特征向量上训练残差 MLP 分类头。
6. 使用准确率（Accuracy）和二次加权 Kappa 系数（Quadratic Kappa）比较回归、骨干头、MLP 头以及集成预测的性能。

## 实验结果总结

下表总结了实验中的代表性指标。

| 实验分支 / 策略 | 结果说明 |
| --- | --- |
| EfficientNet-B4 多任务骨干网络 | 最佳验证损失：`0.6841`；验证集分类准确率达到 `0.8306`；回归 kappa 系数在记录的 epoch 中达到 `0.8859` |
| 基于 EfficientNet-B4 特征的残差 MLP 头 + SMOTE | 验证集准确率达到 `0.8279`；包含文件如 `best_val_acc0.8251.txt` 和 `best_val_acc0.8279.txt` |
| 带有优化阈值的回归分支 | 在推理 notebook 中，准确率约为 `0.7992-0.8101`；二次 kappa 系数约为 `0.8869-0.8917` |
| 骨干分类分支 | 准确率约为 `0.8265-0.8292`；二次 kappa 系数约为 `0.8791-0.8970` |
| 残差 MLP 分类分支 | 最高准确率达到 `0.8484`；二次 kappa 系数最高达到 `0.9017` |
| 多分支集成 | 准确率约为 `0.8279-0.8388`；二次 kappa 系数最高达到 `0.9040` |

结果表明了比较多条预测路径的原因：MLP 分支提供了最高的准确率记录，而静态集成推理获得了最高的二次 kappa 系数。

## Notebooks 说明

| Notebook 文件 | 作用 |
| --- | --- |
| `notebooks/EfficientNet-B4_multi_task.ipynb` | EfficientNet-B4 的回归和分类联合训练 |
| `notebooks/classify_head_effb4.ipynb` | 特征提取、SMOTE 平衡处理以及残差 MLP 头部训练 |
| `notebooks/dynamic_weight_ensemble.ipynb` | 动态类别相关的权重集成推理 |
| `notebooks/static_weight_ensemble.ipynb` | 静态权重集成推理及变体 |
| `notebooks/test_head_inference.ipynb` | 测试/推理工作流以及各分支性能对比 |

`results/` 目录下保存了实验运行产生的结果和日志文件。

## 数据集结构

为了在本地重新运行这些 notebook，预期的数据集需采用 APTOS 竞赛格式，目录结构如下：

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

## 环境配置

你可以通过以下命令安装核心依赖项：

```bash
pip install -r requirements.txt
```

本实验设计在支持 CUDA 的 PyTorch 环境中运行。请根据本地设置相应地更新 notebook 中的路径和 GPU ID。

## 开源协议

本项目采用 [MIT 许可证](LICENSE) 开源。

---

<a name="english-version"></a>
# ReSMOTE-DR (English Version)

ReSMOTE-DR is a diabetic retinopathy (DR) image-analysis experimental project built around EfficientNet-B4, multi-task regression/classification training, feature-level SMOTE, threshold search, and multi-branch ensemble inference.

The workflow covers backbone training, feature extraction, SMOTE-balanced head training, regression-threshold optimization, and ensemble evaluation for DR severity grading.

## Technical Highlights

- **EfficientNet-B4 backbone**: Uses an ImageNet-pretrained EfficientNet-B4 as the retinal image feature extractor.
- **Multi-task output head**: Trains one regression output for continuous severity prediction and five classification logits for DR grade prediction.
- **Feature-level SMOTE**: Extracts EfficientNet-B4 features, balances them in feature space, and trains a residual MLP classification head.
- **Threshold search**: Optimizes regression-output thresholds for converting continuous predictions into discrete severity grades.
- **Branch comparison**: Evaluates regression, backbone classification, residual MLP classification, and ensemble inference paths.

## Repository Layout

```text
ReSMOTE-DR/
├── notebooks/              # Main training, head-training and inference notebooks
├── results/                # Result files, threshold files and experiment notebooks
├── docs/                   # Method and reproducibility notes
├── requirements.txt        # Python dependencies
├── LICENSE                 # Open-source license
└── README.md               # Project documentation
```

## Method Overview

The experiment workflow is:

1. Load APTOS-style retinal fundus images and resize them to `448 x 448`.
2. Train an EfficientNet-B4 backbone with a multi-task head:
   - Regression output for continuous severity prediction.
   - Five classification logits for severity-class prediction.
3. Optimize thresholds for mapping regression outputs to integer DR grades.
4. Extract EfficientNet-B4 features and apply feature-level SMOTE.
5. Train a residual MLP classification head on the balanced feature vectors.
6. Compare regression, backbone-head, MLP-head, and ensemble predictions with accuracy and quadratic kappa metrics.

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

## License

This project is licensed under the [MIT License](LICENSE).
