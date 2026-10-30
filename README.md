# Comparative Analysis of Transfer Learning Models with Explainable AI for Automated Nail Disease Classification

A research project investigating how pretrained computer-vision models and transfer-learning strategies perform on a small, imbalanced nail-image classification dataset, with explainability and shortcut-learning analysis.

> **Research-use disclaimer:** This work is an experimental screening research study. It is not a medical device and must not be used to diagnose, treat, or make clinical decisions. Predictions require evaluation by qualified medical professionals.

## Overview

Nail appearance can reflect a range of conditions, but automated image classification is challenging because nail diseases may have visually similar presentations and image collections can vary in quality, class balance, and source-specific characteristics.

This project compares six pretrained architectures under three transfer-learning strategies using a consistent data pipeline and grouped cross-validation. Beyond classification performance, the study investigates class-wise and source-wise errors, statistical uncertainty, and whether explanation maps highlight meaningful image regions or potential shortcuts such as padding and watermarks.

The study uses **1,008 nail images across 13 classes**, collected from four online sources. Similar or related images are assigned to the same group before five-fold stratified partitioning to reduce leakage between training and evaluation splits.

## Research Objectives

- Compare CNN and Vision Transformer architectures for nail disease classification.
- Evaluate frozen, partially fine-tuned, and fully fine-tuned transfer-learning strategies.
- Investigate model performance using grouped cross-validation.
- Analyse class-wise and source-wise classification errors.
- Evaluate model interpretability using Grad-CAM and Attention Rollout.
- Investigate potential shortcut learning caused by image padding and watermarks.
- Compare classification performance with computational efficiency.

## Models and Transfer Learning Strategies

Six pretrained architectures were evaluated:

| Architecture | Model Family |
|---|---|
| ResNet-50 | Convolutional Neural Network |
| VGG16 | Convolutional Neural Network |
| DenseNet-121 | Convolutional Neural Network |
| ConvNeXt-Tiny | Convolutional Neural Network |
| ViT-B/16 | Vision Transformer |
| DINOv2 ViT-B/14 | Self-Supervised Vision Transformer |

Each architecture was evaluated using three transfer-learning strategies:

1. **Frozen Feature Extraction:** The pretrained backbone remains frozen while the classification head is trained.
2. **Partial Fine-Tuning:** Selected layers of the pretrained backbone are unfrozen and fine-tuned.
3. **Full Fine-Tuning:** The entire pretrained backbone and classification head are trained.

This resulted in **18 experimental configurations**.

## Dataset

The dataset consists of:

- Total images: **1,008**
- Number of classes: **13**
- Similarity/source groups: **959**
- Image sources: 4

### Classification Categories

1. Onycholysis
2. Half-and-half nails
3. Koilonychia
4. Beau's lines
5. Eczema
6. Splinter haemorrhage
7. Alopecia areata
8. Onychomycosis
9. Psoriasis
10. Darier's disease
11. Bluish nail
12. Clubbing
13. Healthy

### Data Preprocessing

The preprocessing pipeline includes:

- RGB image conversion.
- Square padding using the relevant model's normalization mean.
- Image resizing to 224 × 224 pixels.
- Normalization using pretrained model statistics.
- Data augmentation using random crops, horizontal and vertical flips, rotations, brightness, contrast, and saturation adjustments.
- Batch size of 32.

## Experimental Methodology

### Grouped Stratified 5-Fold Cross-Validation

To reduce data leakage, similar images were grouped before the cross-validation split.

- Fold 0 was used for hyperparameter tuning.
- Folds 1–4 were reserved for held-out confirmation evaluation.
- The reported confirmation evaluation included 807 images.

This approach helps prevent similar or related images from appearing in both training and evaluation partitions.

### Training Configuration

| Parameter | Configuration |
|---|---|
| Optimizer | AdamW |
| Loss Function | Weighted Cross-Entropy |
| Learning Rate Scheduler | Warmup + Cosine Decay |
| Regularization | Label Smoothing |
| Gradient Handling | Gradient Clipping |
| Precision | FP16 Automatic Mixed Precision |
| Batch Size | 32 |
| Input Resolution | 224 × 224 |

### Evaluation Metrics

The models were evaluated using:

- Accuracy
- Balanced Accuracy
- Macro-F1 Score
- Group-level Bootstrap Confidence Intervals
- Fold-wise Performance Variation

Macro-F1 was used as the primary evaluation metric because of the class imbalance in the dataset.

## Explainable AI

Explainability techniques were incorporated to investigate how different architectures make classification decisions.

### Techniques Used

**Grad-CAM (Gradient-weighted Class Activation Mapping)**

Used to visualize important image regions contributing to model predictions, particularly for CNN architectures and selected transformer configurations.

**Attention Rollout**

Used to visualize attention patterns in Vision Transformer architectures.

### Explainability Analysis

The study investigated:

- Saliency map generation.
- Deletion and insertion faithfulness tests.
- Padding-region saliency.
- Agreement between explanation maps from different architectures.
- Watermark sensitivity and occlusion experiments.

These experiments help assess whether the models rely on relevant visual features or potentially misleading image characteristics.

## Experimental Results

The following results represent the pooled held-out confirmation folds (folds 1–4), containing 807 images.

### Best Performing Configurations

| Model | Strategy | Accuracy | Balanced Accuracy | Macro-F1 |
|---|---|---:|---:|---:|
| DINOv2 ViT-B/14 | Full Fine-Tuning | 0.881 | 0.846 | **0.857** |
| DINOv2 ViT-B/14 | Partial Fine-Tuning | 0.880 | 0.855 | 0.856 |
| ViT-B/16 | Full Fine-Tuning | 0.875 | 0.844 | 0.852 |
| ResNet-50 | Full Fine-Tuning | 0.871 | 0.839 | 0.851 |

DINOv2 ViT-B/14 with full fine-tuning achieved the highest reported Macro-F1 score of **0.857**.

However, several other configurations were not statistically significantly lower than the best-performing model.

### Comparison of Fine-Tuning Strategies

| Strategy | Macro-F1 Range |
|---|---:|
| Frozen Feature Extraction | 0.592–0.677 |
| Partial Fine-Tuning | 0.734–0.856 |
| Full Fine-Tuning | 0.793–0.857 |

The results demonstrate the benefits of fine-tuning pretrained representations for the nail disease classification task.

### Computational Efficiency

| Model | Parameters | Checkpoint Size | Inference Latency |
|---|---:|---:|---:|
| ResNet-50 (Full) | 23.5M | 94 MB | 6.3 ms |
| DINOv2 ViT-B/14 (Full) | 85.7M | 343 MB | 20.9 ms |

Although DINOv2 achieved the highest Macro-F1, ResNet-50 provided a more computationally efficient alternative with competitive classification performance.

## Explainability Findings

The explainability experiments revealed several important observations:

- Grad-CAM maps for CNN architectures and DINOv2 showed positive deletion and insertion faithfulness gains over random-map baselines in the reported experiments.
- ViT-B/16 demonstrated comparatively higher saliency on padding regions.
- Explanation maps differed considerably across architectures and techniques.
- Watermark occlusion experiments showed that models were sensitive to watermark regions in the evaluated images.
- Explanation maps alone cannot establish that a model is reasoning like a medical professional.

These findings highlight the importance of evaluating both classification performance and model interpretability.

## Repository Structure

```text
.
├── Dataset/
│   └── dataset_v2_main/
│
├── Scripts/
│   ├── nail_resnet50_test_run.ipynb
│   ├── nail_sweep_convnext_tiny.ipynb
│   ├── nail_sweep_densenet121.ipynb
│   ├── nail_sweep_vgg16.ipynb
│   ├── nail_sweep_vit_b16.ipynb
│   └── notebook4dc7416ebf.ipynb
│
├── Results/
│   ├── nail_ConvNeXt-Tiny_test_results./
│   ├── nail_densenet_test_results./
│   ├── nail_dinov2_b14_test_results./
│   ├── nail_sweep_convnext_tiny./
│   ├── nail_sweep_vgg16./
│   ├── nail_vgg16_test_results./
│   └── nail_vitb16_test_results./
│
├── Project_Report.docx
└── README.md
```

## Technologies and Tools

- Python 3.13
- PyTorch 2.11.0
- timm 1.0.29
- Scikit-learn 1.6.1
- NumPy
- Pandas
- Matplotlib
- Grad-CAM
- Vision Transformers
- Kaggle Notebooks
- NVIDIA Tesla T4 GPU

## Experimental Environment

| Component | Specification |
|---|---|
| Platform | Kaggle Notebooks |
| GPU | NVIDIA Tesla T4 |
| Framework | PyTorch 2.11.0 |
| Python | 3.13 |
| Model Library | timm 1.0.29 |
| Evaluation Library | scikit-learn 1.6.1 |
| Image Resolution | 224 × 224 |

The reported experiments required approximately 9.5 GPU hours, excluding data loading and evaluation.

## Limitations

- The dataset is relatively small and class-imbalanced.
- Images were collected from online sources and may contain source-specific biases.
- Some nail conditions have visually similar characteristics, making classification challenging.
- Watermark analysis was conducted on a limited subset of images.
- Explainability maps do not guarantee clinically meaningful reasoning.
- Further evaluation on larger, independently collected clinical datasets is required.

## Future Work

- Expand the dataset with additional clinically validated nail images.
- Investigate domain adaptation and generalization across unseen image sources.
- Explore additional transformer-based architectures.
- Improve explainability and localization of nail-specific features.
- Evaluate the models on external datasets.
- Investigate deployment on resource-constrained devices.

## Project Report

For detailed methodology, experimental results, statistical analysis, explainability experiments, and references, refer to the [Project Report](Project_Report.docx).

## Team Members

- **Satyam Mittal**
- **Shashwat Singh**
- **Progya Paromita Biswas**

**Project Supervisor:** Kaushik Hariharan G.

**Institution:** Vellore Institute of Technology (VIT)

---

> **Disclaimer:** This project is intended for academic and research purposes only. It is an experimental image-classification study and should not be considered a clinically validated diagnostic system or a substitute for professional medical evaluation.
