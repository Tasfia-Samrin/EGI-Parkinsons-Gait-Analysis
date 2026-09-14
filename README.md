# EGI: Explainable Multi-Model Framework for Early Parkinson's Gait Detection 

### Explainable CNN-Transformer and Early Gait Instability Index (EGI)

This project explores the use of wearable Inertial Measurement Unit (IMU) data, machine learning, and deep learning for the detection of early-stage Parkinson's Disease (PD) from gait.

The main focus of the project is the development of an **Early Gait Instability Index (EGI)** that represents different aspects of gait using clinically motivated features. Alongside the EGI-based machine learning approach, several deep learning models are developed to learn directly from raw IMU signals.

The project also focuses on **explainability**, using SHAP and Transformer attention analysis to understand which gait features and temporal regions contribute to model predictions


## Project Overview

Parkinson's Disease can affect walking, balance, turning, tremor, and coordination. Since some gait abnormalities can be subtle during the early stages of the disease, wearable sensors provide a way to capture movement data for computational analysis.

In this project, bilateral ankle IMU data from the **WearGait-PD** dataset is used to investigate whether machine learning and deep learning models can distinguish early-stage PD participants from healthy controls.

The framework contains two main approaches:

- **Feature-based Machine Learning:** EGI features are extracted from gait signals and classified using an SVM.
- **Deep Learning:** Raw IMU signal windows are used directly with CNN, CNN-Transformer, and ResNet-based models.

---

## Dataset

The project uses the publicly available **WearGait-PD** dataset.

For this study, participants with early-stage Parkinson's Disease were selected using the Hoehn & Yahr (H&Y) staging criteria.

### Final study cohort

- **68** early-stage PD participants
- **82** healthy controls
- **149 subjects** in total
- H&Y stages **1, 1.5, and 2** for the PD group
- Bilateral ankle IMU sensors
- **12 IMU channels**
- Sampling frequency: **100 Hz**
- Self-paced walking and Timed Up and Go (TUG) recordings

The 12 channels consist of 3-axis accelerometer and 3-axis gyroscope measurements from both ankles.


## Early Gait Instability Index (EGI)

The main feature-engineering component of this project is the **Early Gait Instability Index (EGI)**.

EGI organizes gait information into five clinically motivated components:

1. **Variability**
2. **Asymmetry**
3. **Tremor**
4. **Timing**
5. **Turning**

These components are calculated from the bilateral ankle IMU signals and are used to create an interpretable representation of gait.

An SVM classifier is then trained using the EGI-derived features.

The EGI-based approach is also compared with a raw-feature SVM baseline to investigate whether clinically organized gait features improve classification performance.

---

## Machine Learning and Deep Learning Models

Several models were implemented and compared.

### 1. Raw SVM

A baseline Support Vector Machine (SVM) using raw statistical gait features.

### 2. SVM + EGI

An RBF-kernel SVM trained using the EGI-derived gait features.

### 3. 1D Residual CNN

A one-dimensional Convolutional Neural Network using residual blocks and Squeeze-and-Excitation (SE) attention.

### 4. Hybrid CNN-Transformer

A hybrid deep learning architecture that combines CNN-based feature extraction with Transformer layers to learn local and temporal patterns from raw gait signals.

### 5. ResNet-18 + Recurrence Plots

Raw IMU signals are transformed into 2D Recurrence Plot representations and classified using a ResNet-18 model.


## Explainability

Explainability is an important part of the framework.

Two complementary approaches are used:

### SHAP

SHAP (SHapley Additive exPlanations) is applied to the EGI-SVM model to investigate the contribution of the engineered gait features.

### Transformer Attention

Attention maps from the Hybrid CNN-Transformer are analyzed to identify temporal regions of the gait sequence that are particularly informative for classification.

This allows the project to examine not only model performance, but also the features and temporal patterns influencing the predictions.

## Data Processing and Evaluation

The IMU signals are processed into fixed-length gait windows before being used by the models.

For the deep learning branch, the raw IMU windows are used as model inputs without hand-crafted feature engineering.

The models are evaluated using **subject-level Group 5-Fold Cross-Validation**. This prevents data from the same participant from appearing in both training and testing sets and helps reduce data leakage.

Classification thresholds are optimized using **Youden's J statistic** to balance sensitivity and specificity.

The best-performing model is also evaluated separately for H&Y Stage 1 and Stage 2 participants.


## Results

The models showed a progression in performance from the raw SVM baseline to the EGI-based model and then to the deep learning approaches.

| Model | Subject-Level AUC |

| Raw SVM | 0.6485 |
| SVM + EGI | 0.7603 |
| 1D CNN | 0.8228 |
| Hybrid CNN-Transformer | **0.8544** |
| ResNet-18 + Recurrence Plot | 0.8025 |

### Best Model: Hybrid CNN-Transformer

| Metric | Result |
|---|---:|
| AUC | **0.8544** |
| Accuracy | **79.87%** |
| Sensitivity | **80.88%** |
| Specificity | **79.01%** |

The EGI-based SVM also improved over the raw-feature SVM, with subject-level AUC increasing from **0.6485 to 0.7603**.


## Project Pipeline

                    WearGait-PD Dataset
                            |
                            v
                 Bilateral Ankle IMU Data
                            |
                            v
                    Signal Processing
                            |
              +-------------+-------------+
              |                           |
              v                           v
       EGI Feature Branch          Raw IMU Branch
              |                           |
              v                           v
          EGI Features             200 × 12 Windows
              |                           |
              v                 +---------+---------+
          SVM Classifier         |         |         |
              |                 v         v         v
              |              1D CNN   CNN-Transformer  ResNet-18
              |                                      |
              v                                      v
        SHAP Analysis                         Recurrence Plots
              |                                      |
              +------------------+-------------------+
                                 |
                                 v
                   Subject-Level Evaluation
                                 |
                                 v
                       PD vs Healthy Control
