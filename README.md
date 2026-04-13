# Vehicle-Detection-with-YOLOv8




[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![YOLOv8](https://img.shields.io/badge/YOLOv8-Ultralytics-00BFFF.svg)](https://github.com/ultralytics/ultralytics)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

**Vehicle Detection in CCTV Traffic Footage** | Group 9 | AIMS Rwanda

![Qualitative Results](images/qualitative_results.png)

## 📖 Overview

This project trains and evaluates YOLOv8 nano on a real-world vehicle detection dataset from public CCTV cameras in Yogyakarta, Indonesia. The study investigates:

- **Three augmentation strategies** (none, default, heavy) and their impact on detection performance
- **Small vs. large object analysis** using custom IoU-based matching
- **Class imbalance effects** (motorbikes dominate, buses/trucks are rare)

The best model achieves **mAP@50 = 0.928** on the test set while running at **8.2 ms/image**, making it suitable for near-real-time traffic monitoring.

## 📊 Key Results

| Metric | Value |
|--------|-------|
| mAP@50 | **0.928** |
| mAP@50-95 | **0.740** |
| Precision | 0.884 |
| Recall | 0.884 |
| Inference Time | 8.2 ms/image |

**Per-class AP@50:** Car: 0.970 | Truck: 0.914 | Bus: 0.962 | Motorbike: 0.867

## 📂 Dataset

- **Source:** Public CCTV footage from three urban intersections in Yogyakarta, Indonesia
- **Total images:** 1,000 (800 train / 100 val / 100 test)
- **Classes:** Car, Motorbike, Truck, Bus
- **Challenge characteristics:** Low resolution, high-angle perspectives, occlusion, variable lighting

![Class Distribution](images/class_distribution.png)

*Significant class imbalance: Motorbikes (5,007 instances) vs. Buses (295 instances)*

## 🧪 Experiments

### 1. Data Augmentation Strategies

| Strategy | mAP@50 | mAP@50-95 | Precision | Recall |
|----------|--------|-----------|-----------|--------|
| No Augmentation | 0.913 | 0.714 | 0.937 | 0.856 |
| **Default Augmentation** | **0.932** | **0.748** | 0.925 | 0.887 |
| Heavy Augmentation | 0.913 | 0.664 | 0.898 | 0.845 |

**Key finding:** Default augmentation outperforms both baseline and heavy augmentation. Heavy augmentation degrades localization (5.0 point drop on mAP@50-95) due to model capacity constraints (nano backbone, 30 epochs).

![Augmentation Training Curves](images/augmentation_comparison.png)

### 2. Small vs. Large Object Analysis

Objects classified by bounding-box area:
- **Small:** < 32×32 px
- **Medium:** 32–96 px side
- **Large:** > 96×96 px

| Size Category | Precision | Recall | F1 Score | Miss Rate |
|---------------|-----------|--------|----------|-----------|
| Large | 0.959 | 0.972 | **0.959** | 2.8% |
| Medium | 0.869 | 0.885 | 0.877 | 11.5% |
| Small | 0.769 | 0.710 | **0.710** | 29.0% |

**Key finding:** Small object detection is the dominant limitation (29% miss rate). The YOLOv8 nano backbone downsamples to 20×20 feature maps, making 32×32 objects cover only ~1 grid cell.

![Size Analysis](images/size_analysis.png)
![Per-class AP](images/per_class_ap.png)

## 🚀 Getting Started

### Installation

```bash
# Clone repository
git clone https://github.com/YOUR_USERNAME/vehicle-detection-yolov8.git
cd vehicle-detection-yolov8

# Install dependencies
pip install ultralytics roboflow matplotlib seaborn pandas numpy Pillow tqdm
