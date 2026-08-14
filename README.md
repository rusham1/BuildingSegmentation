# Building Segmentation Dataset

This repository contains UAV-based imagery, building segmentation masks, and model prediction results for two study areas in Nepal:

- **Khokana Dataset**
- **Lamatar Dataset**

The dataset is organized for semantic segmentation of buildings and for evaluating and comparing deep-learning segmentation models, including **U-Net with ResNet50**, **SegFormer-B2**, and **TransResUNet**.

## Repository Structure

```text
BuildingSegmentation/
│
├── README.md
│
├── Khokana Dataset/
│   ├── img/
│   ├── masks_human/
│   ├── masks_machine/
│   └── results/
│       ├── best_segformer_jaccardbce_fine_tune/
│       ├── best_transresunet_dicebce_fine_tune/
│       ├── best_unet_jaccardbce_fine_tune/
│       ├── groundtruth/
│       └── original/
│
└── Lamatar Dataset/
    ├── img/
    ├── masks_human/
    ├── masks_machine/
    └── results/
        ├── best_segformer_jaccardbce_freezing/
        ├── best_transresunet_dicebce_fine_tune/
        ├── best_unet_jaccardbce_fine_tune/
        ├── groundtruth/
        └── original/
```

## Dataset Components

### 1. `img/`

Contains the UAV image patches used as input to the segmentation models.

The image and corresponding mask use the same patch identifier. For example:

```text
img/
└── patch_0286_x5120_y1792.png

masks_human/
└── patch_0286_x5120_y1792.png
```

This naming convention allows an image to be directly matched with its segmentation mask.

### 2. `masks_human/`

Contains manually annotated building masks.

These masks serve as the **reference/ground-truth annotations** for training, validation, testing, and evaluation.

### 3. `masks_machine/`

Contains machine-generated building masks. These are kept separately from the manually annotated masks so that human annotations and automatically generated masks can be distinguished.

### 4. `results/`

Contains segmentation outputs and reference images used for model evaluation and visual comparison.

#### `groundtruth/`

Contains ground-truth segmentation masks used for comparison with model predictions.

#### `original/`

Contains the original image patches corresponding to the evaluated samples.

#### Model result folders

The model-specific folders contain prediction results from different segmentation architectures and training/transfer-learning configurations:

- `best_segformer_jaccardbce_fine_tune/` — SegFormer model results using Jaccard + Binary Cross-Entropy loss with fine-tuning.
- `best_segformer_jaccardbce_freezing/` — SegFormer model results using Jaccard + Binary Cross-Entropy loss with frozen layers.
- `best_transresunet_dicebce_fine_tune/` — TransResUNet results using Dice + Binary Cross-Entropy loss with fine-tuning.
- `best_unet_jaccardbce_fine_tune/` — U-Net with ResNet50 encoder results using Jaccard + Binary Cross-Entropy loss with fine-tuning.

The exact result folders available differ between the Khokana and Lamatar datasets.

## Study Areas and Resolution

The repository contains two UAV datasets with different ground sampling distances (GSD):

| Dataset | Study Area | Original GSD | Processing |
|---|---|---:|---|
| Khokana | Khokana, Lalitpur, Nepal | ~0.15 m/pixel | Used at the target resolution |
| Lamatar | Lamatar, Lalitpur, Nepal | ~0.026 m/pixel | Resampled to ~0.15 m/pixel for cross-dataset comparison |

Resampling the Lamatar imagery provides approximately comparable ground coverage per image patch when comparing the two datasets.

## Building Segmentation Task

The primary task is **binary semantic segmentation of buildings** from UAV imagery.

For each image patch:

```text
Input:
    UAV image patch

        ↓

Segmentation Model

        ↓

Output:
    Building segmentation mask
```

The manually annotated masks provide the reference against which model predictions can be evaluated.

## Models

The repository contains results from three segmentation architectures:

### U-Net + ResNet50

A U-Net architecture with a ResNet34 and ResNet50 encoder, used as a convolutional neural-network baseline.

### SegFormer-B2

A transformer-based semantic segmentation architecture used to evaluate transformer performance on UAV building imagery.

### TransResUNet

A hybrid architecture combining convolutional and transformer-style feature extraction for segmentation.

## Training / Transfer-Learning Configurations

The result directories indicate two main approaches:

### Fine-tuning

Model weights are adapted to the target dataset by allowing selected pretrained layers to update during training.

### Freezing

Selected pretrained layers are kept frozen while the remaining layers are trained on the target dataset.

The directory names preserve the configuration used for each experiment.

## Loss Functions

Two loss combinations are represented in the result directory names:

- **Jaccard + BCE** — Jaccard loss combined with Binary Cross-Entropy.
- **Dice + BCE** — Dice loss combined with Binary Cross-Entropy.

These losses are suitable for segmentation tasks where the building and background classes may be imbalanced.

## File Naming Convention

Image and mask filenames follow a patch-based naming scheme:

```text
patch_<ID>_x<X>_y<Y>.png
```

For example:

```text
patch_0286_x5120_y1792.png
```

where:

- `<ID>` identifies the patch.
- `x<X>` indicates the horizontal patch position in the source imagery.
- `y<Y>` indicates the vertical patch position in the source imagery.

The same filename is used across the corresponding image and mask directories.

## Recommended Dataset Pairing

For supervised segmentation, pair files using their common filename:

```text
Khokana Dataset/
├── img/
│   └── patch_XXXX_xYYYY_yZZZZ.png
└── masks_human/
    └── patch_XXXX_xYYYY_yZZZZ.png
```

and similarly:

```text
Lamatar Dataset/
├── img/
│   └── patch_XXXX_xYYYY_yZZZZ.png
└── masks_human/
    └── patch_XXXX_xYYYY_yZZZZ.png
```

This avoids relying on directory order and ensures that each image is matched with the correct annotation.

## Intended Use

This repository is intended for:

- Building semantic segmentation from UAV imagery
- Training and evaluating deep-learning segmentation models
- Comparing CNN, transformer, and hybrid segmentation architectures
- Cross-dataset / cross-resolution transfer-learning experiments
- Visual comparison of predicted masks with ground-truth annotations
- Research on building extraction in UAV imagery

## Data Organization Principle

The repository separates:

1. **Input data** — `img/`
2. **Human annotations** — `masks_human/`
3. **Machine-generated annotations** — `masks_machine/`
4. **Experiment outputs** — `results/`

This separation keeps the original imagery and reference annotations independent from generated predictions and model-specific experimental results.

## Notes

- Do not overwrite files in `masks_human/`; these are the reference annotations.
- Keep model outputs inside their corresponding `results/` subdirectory.
- Preserve the common patch filename when creating new predictions or processed masks.
- When comparing results, use the same image samples and corresponding ground-truth masks across models.
