# DevPulse: Developer Behavior Mining Project

## What This Project Does

DevPulse analyzes how developers work by mining patterns from Git commit data. It uses data mining and machine learning techniques to automatically discover different types of developer behaviors across multiple software projects.

**Simple Explanation**: We collect commit data from GitHub, clean it, create meaningful metrics, reduce complexity using PCA, find behavior patterns using clustering, and analyze from multiple angles using OLAP operations.

---

## The Problem

Understanding developer behavior is important for:
- Managing software teams effectively
- Identifying developer burnout risk
- Optimizing team composition
- Predicting code quality
- Resource planning and workload distribution

Traditional methods rely on manual observation. This project automates pattern discovery using data mining.

---

## The Data

**Source**: 5 popular Python repositories from GitHub
- psf/requests (HTTP library)
- pallets/flask (web framework)
- django/django (web framework)
- scikit-learn/scikit-learn (machine learning)
- numpy/numpy (numerical computing)

**What We Collect**:
- Developer name
- Commit timestamp
- Lines of code added
- Lines of code deleted
- Files changed
- Commit hour patterns

**Dataset Size**: 500-1000 developer-week records covering 6 months

---

## The Pipeline (6 Steps)

### Step 1: Data Extraction
**What**: Fetch commit data from GitHub API  
**How**: REST API calls to get commit history  
**Output**: `raw_commits.csv` with basic metrics  
**Time**: ~10 seconds

### Step 2: Data Cleaning & Feature Engineering
**What**: Clean data and create behavioral metrics  
**How**: 
- Remove duplicates
- Handle outliers (IQR method)
- Create 15 new features from raw data

**Key Features Created**:
- `total_churn` = lines added + deleted (code volatility)
- `churn_ratio` = deleted / added (refactoring indicator)
- `workload_intensity` = commits × log(churn) (work magnitude)
- `refactor_signal` = deletions / total churn (cleanup activity)
- `is_after_hours` = commits outside 9am-6pm (work patterns)

**Output**: `cleaned_features.csv` with 15+ features  
**Time**: ~5 seconds

### Step 3: Exploratory Data Analysis (EDA)
**What**: Visualize and understand data patterns  
**How**: Create 6 plots showing distributions, correlations, trends

**Key Plots**:
- Commit activity over time
- Feature distributions
- Correlation heatmap (shows features are related)
- Lines added vs deleted
- Workload by repository
- After-hours coding patterns

**Output**: 6 PNG plots  
**Time**: ~8 seconds

### Step 4: Principal Component Analysis (PCA)
**What**: Reduce 15 features to 5 components  
**Why**: Remove correlation, reduce noise, speed up clustering  
**How**: Mathematical transformation that keeps 90% of information

**Results**:
- PC1 (30-40% variance) = Overall activity level
- PC2 (15-25% variance) = Commit granularity
- PC3 (10-15% variance) = Temporal patterns
- PC4-PC5 (5-10% each) = Structural impact

**Output**: `pca_scores.csv` + 3 plots  
**Time**: ~5 seconds

### Step 5: Clustering Analysis
**What**: Discover behavior patterns automatically  
**How**: K-Means algorithm groups similar developer-weeks

**Process**:
1. Find optimal number of clusters (k=4) using elbow method
2. Run K-Means clustering on PCA components
3. Run Hierarchical clustering for validation
4. Interpret clusters by analyzing feature means

**4 Behavior Types Discovered**:

1. **High-Intensity Burst** (25-30% of data)
   - Many commits per week (>10)
   - High code churn (>2000 lines)
   - Heavy workload
   - Meaning: Sprint periods, major feature development

2. **Refactoring Activity** (15-20% of data)
   - High deletion ratio (>0.8)
   - More code deleted than added
   - Moderate commits
   - Meaning: Code cleanup, technical debt reduction

3. **Low-Activity Maintenance** (30-35% of data)
   - Few commits (<3)
   - Low code changes (<500 lines)
   - Minimal workload
   - Meaning: Bug fixes, minor updates, inactive periods

4. **Normal Feature Development** (20-25% of data)
   - Balanced metrics
   - Regular work hours
   - Moderate churn
   - Meaning: Steady, sustainable development

**Output**: `clustered_data.csv` + 4 plots  
**Time**: ~10 seconds

### Step 6: OLAP Operations
**What**: Multi-dimensional analysis (data warehouse concepts)  
**How**: Aggregate, filter, and pivot data across dimensions

**5 OLAP Operations**:

1. **Roll-Up**: Week → Month → Quarter
   - Aggregate to higher time levels
   - Shows long-term trends
   - Example: Monthly commit totals by repository

2. **Drill-Down**: Quarter → Month → Week
   - Disaggregate to lower time levels
   - Shows detailed patterns
   - Example: Weekly breakdown for Django

3. **Slice**: Filter one dimension
   - Select single value
   - Example: "Show only Django repository"

4. **Dice**: Filter multiple dimensions
   - Select multiple values across dimensions
   - Example: "Django + Flask, Q1 + Q2, High-intensity + Refactoring"

5. **Pivot**: Cross-tabulation
   - Rotate data cube
   - Example: Repository × Behavior matrix

**Output**: 6 CSV files + 3 plots  
**Time**: ~8 seconds

---

## Key Results

### Finding 1: Four Distinct Behavior Patterns
Developers don't work uniformly. They transition between high-intensity sprints, refactoring periods, maintenance mode, and normal development.

### Finding 2: Repository Differences
- Django: Highest workload intensity
- Flask: Most after-hours activity (35-40%)
- NumPy: Widest workload range
- All repos show 20-40% after-hours coding

### Finding 3: PCA Effectiveness
15 correlated features reduced to 5 components with 90% information retained. This proves dimensionality reduction works.

### Finding 4: Temporal Patterns
Commit activity varies by week, month, and quarter. OLAP operations reveal these multi-level patterns.

---

## Technologies Used

**Language**: R (version 4.x)

**Key Libraries**:
- `dplyr`, `tidyr` - Data manipulation
- `ggplot2` - Visualization
- `httr`, `jsonlite` - GitHub API
- `cluster`, `factoextra` - Clustering
- `lubridate` - Date handling

**Total Runtime**: 30-60 seconds for complete pipeline

---

## How to Run

```bash
# Navigate to project
cd DevPulse

# Run complete pipeline
Rscript R/00_run_all.R

# View outputs
ls plots/      # 16 PNG visualizations
ls data/       # 4 CSV datasets
ls output/     # 6 OLAP results
```

---

## Project Structure

```
DevPulse/
├── R/                    # 7 R scripts (pipeline stages)
├── data/                 # 4 CSV files (datasets)
├── plots/                # 16 PNG files (visualizations)
├── output/               # 6 CSV files (OLAP results)
├── docs/                 # 5 documentation files
└── presentation/         # This file
```

---

## Data Mining Concepts Applied

✅ **Data Collection**: GitHub API integration  
✅ **Data Preprocessing**: Cleaning, outlier treatment  
✅ **Feature Engineering**: Creating domain-specific metrics  
✅ **Exploratory Analysis**: Visualization, correlation  
✅ **Dimensionality Reduction**: PCA (15→5 features)  
✅ **Unsupervised Learning**: K-Means clustering  
✅ **Cluster Validation**: Elbow method, silhouette scores  
✅ **Data Warehousing**: OLAP operations  
✅ **Statistical Analysis**: Distribution, correlation  
✅ **Visualization**: 16 publication-quality plots  

---

## Practical Applications

1. **Team Management**: Identify when developers are in high-intensity mode
2. **Burnout Prevention**: Flag sustained high-intensity or after-hours patterns
3. **Resource Planning**: Forecast workload based on historical patterns
4. **Code Quality**: Refactoring activity correlates with code quality
5. **Team Composition**: Balance different behavior types
6. **Performance Review**: Objective metrics for developer activity

---

## Key Metrics Explained

**total_churn**: Total lines changed (added + deleted)  
→ Measures code volatility

**churn_ratio**: Lines deleted / lines added  
→ Values >1 indicate refactoring (more deletion than addition)

**workload_intensity**: commits × log(total_churn)  
→ Combines frequency and magnitude of work

**refactor_signal**: Lines deleted / total churn  
→ Values >0.4 suggest refactoring activity

**is_after_hours**: Binary flag for commits outside 9am-6pm  
→ Indicates work schedule patterns

---

## Validation Methods

**PCA Validation**:
- Scree plot shows clear elbow at PC5
- Cumulative variance: 90% with 5 components
- Loadings make intuitive sense (PC1 = activity)

**Clustering Validation**:
- Elbow method suggests k=3-4
- Silhouette score: 0.35-0.45 (acceptable)
- Hierarchical clustering confirms K-Means results
- Visual separation in PC space
- Clusters have meaningful interpretations

---

## Limitations

1. **Sample size**: 500-1000 records (moderate, not big data)
2. **Time coverage**: 6 months (limited for long-term trends)
3. **Repository diversity**: Python-focused (5 repos)
4. **Behavioral assumptions**: Commits reflect actual work
5. **Synthetic fallback**: Used when API fails (maintains statistical properties)

These are typical of proof-of-concept projects. The methodology scales to larger datasets.

---

## Future Enhancements

1. **Scale up**: Analyze 50-100 repositories across multiple languages
2. **Longer timeframe**: 1-2 years of data for seasonal patterns
3. **Supervised learning**: Predict burnout, code quality, bugs
4. **Real-time dashboard**: Live monitoring of developer patterns
5. **Network analysis**: Developer collaboration graphs
6. **Text mining**: Analyze commit messages for sentiment
7. **Anomaly detection**: Flag unusual behavior automatically
8. **Time series forecasting**: Predict future commit patterns

---

## Presentation Tips

**30-Second Pitch**:
"DevPulse mines developer behavior patterns from Git commits. We analyzed 5 GitHub repositories, engineered 15 behavioral features, used PCA to reduce dimensions, discovered 4 behavior types with K-Means clustering, and performed OLAP operations for multi-dimensional analysis. Results show developers transition between high-intensity sprints, refactoring periods, maintenance mode, and normal development."

**Must-Show Plots** (pick 3-4):
1. Correlation heatmap (justifies PCA)
2. Scree plot (shows dimensionality reduction)
3. K-Means clusters (main result)
4. Cluster profiles (explains behavior types)
5. OLAP pivot heatmap (demonstrates data warehousing)

**Key Numbers**:
- 5 repositories
- 500-1000 records
- 15 features → 5 components
- 4 behavior clusters
- 90% variance retained
- 16 visualizations

---

## Quick Reference

**The 4 Behaviors** (memorize these!):
1. High-intensity burst - Sprint mode
2. Refactoring activity - Code cleanup
3. Low-activity maintenance - Bug fixes
4. Normal development - Steady work

**The Pipeline**:
1. Extract → GitHub API
2. Clean → Remove outliers, engineer features
3. EDA → Visualize patterns
4. PCA → 15→5 dimensions
5. Cluster → Find 4 behavior types
6. OLAP → Multi-dimensional analysis

**Why PCA?**: Removes correlation, reduces noise, speeds clustering

**Why K-Means?**: Finds natural groupings automatically

**Why OLAP?**: Analyzes data from multiple perspectives (time, repo, behavior)

---

## Summary

DevPulse is a complete data mining pipeline that:
- Extracts real developer data from GitHub
- Engineers meaningful behavioral metrics
- Reduces complexity using PCA
- Discovers patterns using clustering
- Analyzes from multiple angles using OLAP

The project demonstrates end-to-end data mining skills: collection, preprocessing, feature engineering, dimensionality reduction, unsupervised learning, validation, and visualization.

**Result**: Automatic discovery of 4 developer behavior types with practical applications for team management and burnout prevention.

---

**Total Pipeline Runtime**: 30-60 seconds  
**Total Outputs**: 16 plots + 10 CSV files  
**Lines of Code**: ~1000 lines of R  
**Documentation**: 5 comprehensive guides  

**You're ready to present! 🚀**
