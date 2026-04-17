# DevPulse: Developer Behavior Mining & Analysis

## Project Overview

**DevPulse** is a comprehensive Data Mining and Data Warehouse project that analyzes developer behavior patterns from Git commit data across multiple repositories. The project applies advanced data mining techniques including dimensionality reduction (PCA), clustering algorithms, and OLAP operations to discover meaningful patterns in software development activities.

---

## 🎯 Project Objectives

1. **Extract** developer activity data from GitHub repositories
2. **Clean and engineer** behavioral features from raw commit data
3. **Explore** patterns through statistical analysis and visualization
4. **Reduce dimensionality** using Principal Component Analysis (PCA)
5. **Discover clusters** of similar developer behaviors using K-Means and Hierarchical Clustering
6. **Perform OLAP operations** for multi-dimensional analysis (Roll-Up, Drill-Down, Slice, Dice, Pivot)

---

## 📊 Data Sources

The project analyzes commit data from **5 popular Python repositories**:

- **psf/requests** - HTTP library for Python
- **pallets/flask** - Lightweight web framework
- **django/django** - High-level web framework
- **scikit-learn/scikit-learn** - Machine learning library
- **numpy/numpy** - Numerical computing library

**Data Collection Method**: GitHub REST API (with synthetic fallback for rate-limiting)

---

## 🔄 Pipeline Architecture

The project follows a **6-stage sequential pipeline**:

```
Stage 1: Data Extraction
    ↓
Stage 2: Data Cleaning & Feature Engineering
    ↓
Stage 3: Exploratory Data Analysis (EDA)
    ↓
Stage 4: Principal Component Analysis (PCA)
    ↓
Stage 5: Clustering Analysis
    ↓
Stage 6: OLAP Operations
```

### Execution
Run the complete pipeline with:
```r
Rscript R/00_run_all.R
```

---

## 📁 Project Structure

```
DevPulse/
├── R/                          # Analysis scripts
│   ├── 00_run_all.R           # Master pipeline orchestrator
│   ├── 01_data_extraction.R   # GitHub API data collection
│   ├── 02_data_cleaning_features.R  # Data preprocessing
│   ├── 03_eda.R               # Exploratory analysis
│   ├── 04_pca.R               # Dimensionality reduction
│   ├── 05_clustering.R        # Pattern discovery
│   └── 06_olap_operations.R   # Multi-dimensional analysis
├── data/                       # Processed datasets
│   ├── raw_commits.csv        # Raw extraction
│   ├── cleaned_features.csv   # Engineered features
│   ├── pca_scores.csv         # PCA components
│   └── clustered_data.csv     # Labeled clusters
├── plots/                      # 16 visualization outputs
├── output/                     # OLAP analysis results
└── docs/                       # Documentation
```

---

## 🔍 Key Features & Metrics

### Raw Metrics (from Git commits)
- **commits**: Number of commits per developer per week
- **lines_added**: Total lines of code added
- **lines_deleted**: Total lines of code removed
- **files_changed**: Number of files modified
- **mean_hour**: Average commit time (hour of day)
- **hour_std**: Standard deviation of commit times

### Engineered Features
- **total_churn**: lines_added + lines_deleted (code volatility)
- **churn_ratio**: lines_deleted / lines_added (deletion intensity)
- **net_lines**: lines_added - lines_deleted (net contribution)
- **avg_lines_per_commit**: Average code change size
- **avg_files_per_commit**: Scope of changes
- **is_after_hours**: Binary flag for commits outside 9am-6pm
- **risk_intensity**: Composite metric of change volatility
- **workload_intensity**: commits × log(total_churn)
- **refactor_signal**: Proportion of deletions (refactoring proxy)

---

## 📈 Analysis Outputs

### 1. Exploratory Data Analysis (6 plots)
- Commit activity timeline by repository
- Feature distribution histograms
- Correlation heatmap
- Lines added vs deleted scatter plot
- Workload intensity boxplots
- After-hours coding patterns

### 2. Principal Component Analysis (3 plots)
- Scree plot (variance explained)
- Biplot (PC1 vs PC2 by repository)
- Loading vectors (feature contributions)

**Key Finding**: First 5 PCs explain ~90% of variance

### 3. Clustering Analysis (4 plots)
- Elbow plot (optimal k selection)
- K-Means cluster visualization
- Hierarchical dendrogram
- Cluster profile comparison

**Discovered Behavior Patterns**:
- **High-intensity burst**: Heavy workload, many commits
- **Refactoring activity**: High deletion ratio
- **Low-activity maintenance**: Minimal changes
- **Normal feature development**: Balanced metrics

### 4. OLAP Operations (3 plots + 6 CSV outputs)
- Roll-up: Week → Month → Quarter aggregation
- Drill-down: Quarter → Month → Week detail
- Slice: Single repository, single quarter
- Dice: Multi-dimensional filtering
- Pivot: Repository × Behavior matrix

---

## 🛠️ Technologies Used

**Language**: R (version 4.x)

**Key Libraries**:
- **Data manipulation**: dplyr, tidyr, lubridate
- **Visualization**: ggplot2, scales, ggrepel
- **Clustering**: cluster, factoextra, dendextend
- **API access**: httr, jsonlite

---

## 📊 Sample Results

### Cluster Distribution
Typical output identifies 3-6 distinct developer behavior clusters:
- Cluster 1: High-intensity developers (frequent, large commits)
- Cluster 2: Refactoring specialists (high deletion ratio)
- Cluster 3: Maintenance mode (low activity)
- Cluster 4: Balanced contributors (normal patterns)

### OLAP Insights
- **Temporal patterns**: Commit volume peaks mid-week
- **Repository differences**: Django shows highest workload intensity
- **Behavioral shifts**: Developers transition between states over time

---

## 🎓 Data Mining Concepts Applied

1. **Data Preprocessing**: Cleaning, outlier treatment, normalization
2. **Feature Engineering**: Domain-specific metric creation
3. **Dimensionality Reduction**: PCA for latent structure discovery
4. **Unsupervised Learning**: K-Means and Hierarchical Clustering
5. **Data Warehousing**: OLAP cube operations
6. **Statistical Analysis**: Correlation, distribution analysis
7. **Data Visualization**: Multi-dimensional plotting

---

## 📝 How to Present This Project

### Introduction (2 min)
- Problem: Understanding developer behavior patterns
- Goal: Mine insights from Git commit data
- Approach: End-to-end data mining pipeline

### Methodology (5 min)
- Data extraction from GitHub API
- Feature engineering (explain 2-3 key features)
- PCA for dimensionality reduction
- Clustering for pattern discovery
- OLAP for business intelligence

### Results (5 min)
- Show 3-4 key visualizations
- Explain discovered behavior clusters
- Demonstrate OLAP operations

### Conclusion (2 min)
- Insights gained
- Practical applications
- Future enhancements

---

## 🚀 Running the Project

1. **Install R** (version 4.0+)
2. **Navigate to project directory**:
   ```bash
   cd DevPulse
   ```
3. **Run pipeline**:
   ```r
   Rscript R/00_run_all.R
   ```
4. **View outputs**:
   - Plots: `plots/*.png`
   - Data: `data/*.csv`
   - OLAP results: `output/*.csv`

**Note**: Required packages are auto-installed on first run.

---

## 📚 Further Reading

For detailed explanations of specific concepts, see:
- [Technical Terms Glossary](GLOSSARY.md)
- [Detailed Methodology](METHODOLOGY.md)
- [Results Interpretation Guide](RESULTS_GUIDE.md)

---

**Project Type**: Data Mining & Data Warehouse Course Project  
**Domain**: Software Engineering Analytics  
**Techniques**: PCA, K-Means, Hierarchical Clustering, OLAP
