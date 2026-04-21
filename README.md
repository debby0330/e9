# Week 9: Change Detection & Validation — ARIA v6.0
## 第九週：變化偵測與驗證 — ARIA v6.0（經過驗證的稽核員）

**Course**: 遙測與空間資訊之分析與應用 | Remote Sensing Analysis & Applications  
**Institution**: National Taiwan University (NTU)  
**Instructor**: Prof. Su Wen-Ray 
**Case Study**: Matai'an Barrier Lake (Typhoon Colo)  

---

## 📋 Overview / 概述

This notebook implements **ARIA v6.0 (Auditor + Rater + Indicator + Advisor)** framework for detecting and validating water body changes using Sentinel-2 satellite imagery. The study focuses on the Matai'an barrier lake formation and drainage following Typhoon Colo in 2025.

本筆記本實現 **ARIA v6.0（稽核員 + 評分員 + 指標 + 顧問）** 框架，用於偵測和驗證使用 Sentinel-2 衛星影像的水體變化。研究重點是 2025 年科羅颱風後馬太安堰塞湖的形成和排水。

---

## 🎯 Learning Objectives / 學習目標

### Lab 1: Difference Mapping (35 min) / 實驗1：差異圖製作
- Compute spectral indices (NDVI, NDWI) for three temporal scenes
- Create difference maps to identify water body changes
- Explore threshold sensitivity for water detection

### Lab 2: Accuracy Assessment & Validation (50 min) / 實驗2：精度評估與驗證
- Load and process ground truth validation points
- Compute confusion matrix and accuracy metrics
- Generate validation reports using ARIA v6.0 framework

---

## 🗂️ Dataset / 資料集

### Satellite Imagery / 衛星影像
- **Source**: Sentinel-2 Level-2A (MSI)
- **Spatial Resolution**: 10 meters
- **Study Area**: Matai'an Barrier Lake [121.28, 23.56, 121.52, 23.76]
- **Temporal Coverage**: 
  - Pre-event: June 15, 2025
  - Mid-event: September 11, 2025 (lake formation)
  - Post-event: October 16, 2025 (lake drainage)

### Validation Data / 驗證資料
- **File**: `data/validation_points.geojson`
- **Points**: 60 field-corrected validation points
  - 15 lake points
  - 15 landslide points  
  - 30 stable points
- **Source**: Instructor-provided ground truth with VHR imagery correction

---

## 🛠️ Dependencies / 依賴套件

```python
# Core scientific computing
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Remote sensing and geospatial
import pystac_client
import stackstac
import planetary_computer as pc
from pyproj import Transformer

# Machine learning and validation
from sklearn.metrics import confusion_matrix, classification_report

# Utilities
import json
import os
from datetime import datetime
import warnings
warnings.filterwarnings('ignore')
```

### Installation / 安裝
```bash
pip install numpy pandas matplotlib seaborn pystac-client stackstac planetary-computer scikit-learn pyproj
```

---

## 📊 Key Methods / 主要方法

### Spectral Indices / 光譜指標
1. **NDVI (Normalized Difference Vegetation Index)**
   ```
   NDVI = (NIR - Red) / (NIR + Red)
   ```
   - Sensitive to vegetation density
   - Range: [-1, 1], higher values = more vegetation

2. **NDWI (Normalized Difference Water Index)**
   ```
   NDWI = (Green - NIR) / (Green + NIR)
   ```
   - Optimized for water body detection
   - Range: [-1, 1], positive values = water presence

### Change Detection / 變化偵測
- **Difference Maps**: ΔIndex = Time₂ - Time₁
- **Threshold Classification**: Water if NDWI > threshold
- **Threshold Sensitivity Analysis**: Test multiple thresholds (0.00 to 0.15)

### Accuracy Assessment / 精度評估
- **Confusion Matrix**: TP, TN, FP, FN
- **Producer's Accuracy** (Recall): TP / (TP + FN)
- **User's Accuracy** (Precision): TP / (TP + FP)
- **Overall Accuracy**: (TP + TN) / Total
- **Cohen's Kappa**: Agreement beyond chance

---

## 🚀 Usage / 使用方法

### 1. Environment Setup / 環境設定
```python
# Run the first cell to set up environment and load configurations
# This includes STAC client setup, bbox definitions, and output directory creation
```

### 2. Load Satellite Data / 載入衛星資料
```python
# Cells [S2]-[S3] load three temporal scenes from Microsoft Planetary Computer
# Includes cloud masking using SCL (Scene Classification Layer)
```

### 3. Compute Indices / 計算指標
```python
# Cell [S4] calculates NDVI and NDWI for all three scenes
# Applies cloud masks and generates three-act visualization panels
```

### 4. Change Detection Analysis / 變化偵測分析
```python
# Cell [S5] creates difference maps between temporal scenes
# Cell [S6] performs threshold sensitivity analysis
```

### 5. Validation / 驗證
```python
# Cells [S7]-[S11] implement the complete validation workflow
# Includes mask creation, point sampling, confusion matrix, and metrics calculation
```

---

## 📈 Results & Findings / 結果與發現

### Key Performance Metrics / 主要性能指標
- **Overall Accuracy**: 80.0%
- **Producer's Accuracy**: 20.0% (Recall)
- **User's Accuracy**: 100.0% (Precision)
- **Cohen's Kappa**: 0.273

### Threshold Sensitivity / 臨界值敏感性
- **Optimal Threshold**: 0.10 for water detection
- **Trade-off**: Higher thresholds increase specificity but decrease sensitivity
- **Use Case Dependent**: 
  - Disaster Alert: Low threshold (0.05)
  - Scientific Archive: High threshold (0.15)

### Index Comparison / 指標比較
- **NDWI**: More robust for water detection, less affected by shoreline vegetation
- **NDVI**: Sensitive to vegetation changes but less specific to water bodies

---

## 📁 Output Files / 輸出檔案

All outputs are saved to the `output/` directory:

1. **`W9_L1_ndvi_three_acts.png`** - NDVI temporal comparison panel
2. **`W9_L1_ndwi_three_acts.png`** - NDWI temporal comparison panel  
3. **`W9_L1_difference_maps.png`** - 2×2 difference map panel
4. **`W9_L1_threshold_sensitivity.png`** - Threshold sensitivity curve
5. **`W9_L2_masks.png`** - Binary water masks visualization
6. **`W9_L2_confusion_matrix.png`** - Confusion matrix heatmap

---

## 🎓 Educational Notes / 教學說明

### ARIA v6.0 Framework / ARIA v6.0 框架
1. **Auditor (稽核員)**: Quality control and data validation
2. **Rater (評分員)**: Accuracy assessment and metrics calculation
3. **Indicator (指標)**: Change detection and threshold analysis
4. **Advisor (顧問)**: Decision support and recommendations

### Ground Truth Principles / 真實地面原則
1. **Independence**: Validation must come from independent sources
2. **Stratified Sampling**: Distribute points across all zones
3. **Temporal Matching**: Ground truth date close to satellite acquisition
4. **Sufficient Sample Size**: Minimum 30-50 points for reliable validation

---

## 🔧 Troubleshooting / 故障排除

### Common Issues / 常見問題

1. **Memory Issues / 記憶體問題**
   - Reduce `chunksize` parameter in `stackstac.stack()`
   - Process smaller AOI regions

2. **Cloud Masking / 雲遮罩問題**
   - Always apply SCL masking before change detection
   - Check cloud percentage statistics in cell [S3b]

3. **Coordinate Transformation / 座標轉換問題**
   - Ensure EPSG:4326 (lon/lat) to EPSG:32651 (UTM) transformation
   - Verify bbox coordinates match study area

4. **Threshold Selection / 臨界值選擇**
   - Use threshold sensitivity analysis to inform decision
   - Consider specific use case requirements

---

## 📚 References / 參考文獻

1. **McFeeters, S.K.** (1996). The use of the Normalized Difference Water Index (NDWI) in the delineation of open water features. *International Journal of Remote Sensing*.

2. **Rouse, J.W., et al.** (1973). Monitoring vegetation systems in the Great Plains with ERTS. *NASA Special Publication*.

3. **Microsoft Planetary Computer** (2024). Sentinel-2 Level-2A data access and processing.

4. **National Taiwan University** (2025). Remote Sensing Analysis & Applications course materials.

---

## 👥 Contributors / 貢獻者

- **Course Instructor**: Prof. Su Wen-Ray (蘇文瑄教授)
- **Teaching Assistant**: [TA Name]
- **Student**: [Your Name]

---

## 📄 License / 授權

This educational notebook is provided for academic purposes. Please cite appropriately if used in research or publications.

---

## 🔄 Version History / 版本歷史

- **v1.0** (April 2025): Initial implementation for Week 9 lab
- **v1.1** (April 2025): Added validation framework and ARIA v6.0 integration
- **v1.2** (April 2025): Enhanced documentation and troubleshooting guide

---

*Last Updated: April 2025*  
*Course: 遙測與空間資訊之分析與應用 | Remote Sensing Analysis & Applications*  
*National Taiwan University*
