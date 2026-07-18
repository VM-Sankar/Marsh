# Dry Bean Clustering Analysis

This project explores the **Dry Bean Dataset** using unsupervised machine learning (clustering) techniques. The goal is to group seven different types of dry beans based on their geometric and morphological features, evaluate the performance of different clustering algorithms, and visualize the resulting clusters in 2D space.

## Table of Contents
- [Project Overview](#project-overview)
- [Dataset Details](#dataset-details)
- [Project Workflow](#project-workflow)
  - [1. Exploratory Data Analysis (EDA)](#1-exploratory-data-analysis-eda)
  - [2. Data Preprocessing](#2-data-preprocessing)
  - [3. Hierarchical (Agglomerative) Clustering](#3-hierarchical-agglomerative-clustering)
  - [4. DBSCAN Clustering](#4-dbscan-clustering)
  - [5. Dimensionality Reduction & Visualization (PCA)](#5-dimensionality-reduction--visualization-pca)
- [Key Findings & Performance Metrics](#key-findings--performance-metrics)
- [Dependencies & Setup](#dependencies--setup)

---

## Project Overview

Dry beans have various shapes and sizes depending on their uniform classification. In this project, we implement two primary clustering techniques to segment the dataset:
1. **Agglomerative Hierarchical Clustering** (using Ward's linkage)
2. **DBSCAN** (Density-Based Spatial Clustering of Applications with Noise)

Additionally, we leverage **Principal Component Analysis (PCA)** to project the high-dimensional feature space onto a 2D plane to visually inspect the clusters.

---

## Dataset Details

The dataset contains a total of **13,611 samples** of dry beans belonging to **7 distinct classes**:
* `DERMASON` (3,546)
* `SIRA` (2,636)
* `SEKER` (2,027)
* `HOROZ` (1,928)
* `CALI` (1,630)
* `BARBUNYA` (1,322)
* `BOMBAY` (522)

### Features
Each sample has **16 morphological features** extracted from digital images:
1. **Area ($A$):** The area of a bean zone and the number of its pixels.
2. **Perimeter ($P$):** Bean circumference defined as the length of its border.
3. **MajorAxisLength ($L$):** The distance between the ends of the longest line that can be drawn from a bean.
4. **MinorAxisLength ($l$):** The longest line that can be drawn from the bean while standing perpendicular to the main axis.
5. **AspectRation ($K$):** Defines the relationship between the MajorAxisLength and MinorAxisLength.
6. **Eccentricity ($Ec$):** Eccentricity of the ellipse having the same moments as the region.
7. **ConvexArea ($C$):** Number of pixels in the smallest convex polygon that can contain the area of a bean seed.
8. **EquivDiameter ($Ed$):** The diameter of a circle having the same area as a bean seed area.
9. **Extent ($Ex$):** The ratio of the pixels in the bounding box to the bean area.
10. **Solidity ($S$):** The ratio of the pixels in the convex shell to those in the bean area (convexity).
11. **roundness ($R$):** Calculated with the formula: $\frac{4 \pi A}{P^2}$.
12. **Compactness ($CO$):** Measures the roundness of the object: $\frac{Ed}{L}$.
13. **ShapeFactor1 ($SF1$)**
14. **ShapeFactor2 ($SF2$)**
15. **ShapeFactor3 ($SF3$)**
16. **ShapeFactor4 ($SF4$)**

---

## Project Workflow

### 1. Exploratory Data Analysis (EDA)
- **Shape & Completeness**: Checked dataset dimensions and verified there are zero missing values.
- **Duplicates**: Identified and analyzed 68 duplicate records.
- **Feature Relationships**: Generated a Correlation Matrix Heatmap to analyze multicollinearity among the geometric features (e.g., strong correlations between Area, Perimeter, ConvexArea, and EquivDiameter).

### 2. Data Preprocessing
- **Feature Scaling**: Scaled all 16 features using `StandardScaler` to ensure that features with larger magnitudes (like Area and Perimeter) do not dominate distance calculations.

### 3. Hierarchical (Agglomerative) Clustering
- **Dendrogram Visualization**: Created a dendrogram on a representative subset of 1,000 samples using **Ward Linkage** to understand cluster divisions.
- **Hyperparameter Optimization**: Evaluated cluster counts $k \in [2, 10]$ based on the Silhouette Score.
  - *Best Silhouette Score*: **0.4044** at **$k = 3$**.
  - *Silhouette Score at target class count ($k = 7$)*: **0.2838**.

### 4. DBSCAN Clustering
- **Epsilon Estimation**: Fit a `NearestNeighbors` model ($k=5$) and plotted the sorted distances of the 4th nearest neighbor to find the optimal elbow point for `eps`.
- **Model Tuning**: Implemented DBSCAN with `eps = 1.2` and `min_samples = 5`.
  - *Noise Detection*: Labeled **379 samples (2.78%)** as noise/outliers (cluster label `-1`).

### 5. Dimensionality Reduction & Visualization (PCA)
- Reduced the standard-scaled feature space to **2 Principal Components** using PCA to visualize both the Agglomerative Clusters and DBSCAN clusters on a 2D scatter plot.

---

## Key Findings & Performance Metrics

### Unsupervised Clustering Quality

| Algorithm | Clusters ($k$) | Silhouette Score | Davies-Bouldin Index |
| :--- | :---: | :---: | :---: |
| **Agglomerative Clustering** | 3 | **0.4044** | — |
| **Agglomerative Clustering** | 7 | 0.2838 | 1.1064 |
| **DBSCAN** (excl. noise) | 5 | **0.4109** | **0.8153** |

*Note: DBSCAN outperforms Agglomerative Clustering on both the Silhouette and Davies-Bouldin metrics when outlier noise is filtered out.*

### External Validation (Comparison to True Bean Classes)
For the 7-cluster Agglomerative model, we evaluated clustering alignment against the true categories:
* **Adjusted Rand Index (ARI)**: **0.6580**
* **Normalized Mutual Information (NMI)**: **0.7325**

#### Cluster-Class Confusion Matrix (Cross-Tabulation):
* **Cluster 5** exhibits **100% purity** for the `BOMBAY` bean class (all 522 samples clustered perfectly with no other classes present).
* `DERMASON` (Cluster 2) and `SEKER` (Cluster 3) are also clustered with high purity.
* `SIRA` (Cluster 4) and `CALI` (Cluster 0) show moderate overlap with other geometrically similar bean classes.

---

## Dependencies & Setup

### Prerequisites
Make sure you have Python installed along with the following libraries:
```bash
pip install pandas numpy scikit-learn matplotlib scipy openpyxl
```

### Execution
1. Place the dataset file `Dry_Bean_Dataset.xlsx` in the same directory as the notebook.
2. Open and run the Jupyter notebook:
```bash
jupyter notebook db.ipynb
```
