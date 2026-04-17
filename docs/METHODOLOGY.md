# Detailed Methodology

## Pipeline Stages Explained

---

## Stage 1: Data Extraction

### Purpose
Collect raw developer activity data from GitHub repositories.

### Process
1. **Target Selection**: 5 popular Python repositories chosen for diversity
   - Different project sizes
   - Different development patterns
   - Active communities

2. **API Interaction**:
   - Uses GitHub REST API endpoint: `/repos/{owner}/{repo}/commits`
   - Fetches up to 300 commits per repository (3 pages × 100 commits)
   - Extracts per commit:
     - Developer username
     - Commit timestamp
     - Lines added/deleted
     - Files changed

3. **Fallback Strategy**:
   - If API fails (rate limiting, network issues), generates synthetic but realistic data
   - Synthetic data maintains statistical properties of real commits
   - Uses probabilistic models based on developer archetypes

4. **Aggregation**:
   - Groups commits by developer and week
   - Calculates weekly totals for each metric
   - Adds temporal features (mean commit hour, hour standard deviation)

### Output
`data/raw_commits.csv` with columns:
- repo, developer, week, commits, lines_added, lines_deleted, files_changed, mean_hour, hour_std

### Data Quality
- Typical output: 500-1000 developer-week records
- Covers 6 months of activity
- 8-15 developers per repository

---

## Stage 2: Data Cleaning & Feature Engineering

### Purpose
Transform raw data into analysis-ready features that capture developer behavior patterns.

### Cleaning Steps

#### 1. Duplicate Removal
- Identifies duplicate (repo, developer, week) combinations
- Keeps first occurrence
- Typical removal: 0-5% of records

#### 2. Non-Negative Enforcement
- Ensures all count metrics ≥ 0
- Corrects any data extraction errors

#### 3. Outlier Treatment
- Uses IQR (Interquartile Range) method
- Caps extreme values at Q1 - 3×IQR and Q3 + 3×IQR
- Applied to: commits, lines_added, lines_deleted, files_changed
- Preserves 99%+ of data while removing extreme anomalies

#### 4. Missing Value Handling
- Removes records where all core metrics are zero (inactive weeks)
- Imputes missing temporal features with median values

### Feature Engineering

#### Core Churn Metrics
```r
total_churn = lines_added + lines_deleted
```
Measures total code volatility regardless of direction.

```r
churn_ratio = lines_deleted / lines_added
```
High values (>1) indicate net deletion; useful for detecting refactoring.

```r
net_lines = lines_added - lines_deleted
```
Net contribution to codebase size.

#### Productivity Signals
```r
avg_lines_per_commit = total_churn / commits
```
Commit granularity: small values = frequent small commits, large values = infrequent large commits.

```r
avg_files_per_commit = files_changed / commits
```
Scope of changes: how many files touched per commit.

#### Temporal Patterns
```r
is_after_hours = (mean_hour < 9 OR mean_hour > 18)
```
Binary flag for non-standard work hours.

```r
hour_normalized = (mean_hour - 9) / 9
```
Scales work time to 0-1 range (9am = 0, 6pm = 1).

#### Structural Impact
```r
file_spread = log(files_changed + 1)
```
Log-transformed file count; reduces skew from occasional massive changes.

```r
commit_density = log(commits + 1)
```
Log-transformed commit frequency.

#### Risk Signals
```r
risk_intensity = (total_churn / (files_changed + 1)) × (hour_std / 5)
```
Combines change magnitude per file with temporal irregularity.

```r
workload_intensity = commits × log(total_churn + 1)
```
Composite metric capturing both frequency and magnitude.

#### Refactoring Proxy
```r
refactor_signal = lines_deleted / total_churn
```
Proportion of deletions; values >0.4 suggest refactoring activity.

### Categorical Encoding
- `repo_id`: Integer encoding of repository names
- `dev_id`: Integer encoding of (repo, developer) pairs

### Output
`data/cleaned_features.csv` with 20+ columns including all engineered features.

---

## Stage 3: Exploratory Data Analysis (EDA)

### Purpose
Understand data distributions, relationships, and patterns before advanced analysis.

### Analyses Performed

#### 1. Temporal Trends (Plot 01)
- Weekly commit volume by repository
- LOESS smoothing to identify trends
- Reveals project activity cycles

#### 2. Feature Distributions (Plot 02)
- Histograms of key metrics
- Faceted by repository
- Identifies skewness, modality, outliers

#### 3. Correlation Analysis (Plot 03)
- Pearson correlation matrix for 13 features
- Heatmap visualization
- Identifies multicollinearity (justifies PCA)
- Key findings:
  - Strong correlation: commits ↔ total_churn (0.7-0.8)
  - Moderate correlation: workload_intensity ↔ commits (0.6-0.7)
  - Weak correlation: is_after_hours ↔ other metrics (0.1-0.2)

#### 4. Add vs Delete Scatter (Plot 04)
- Log-scale scatter plot
- Diagonal line = equal add/delete
- Points above line = net deletion (refactoring)
- Points below line = net addition (new features)

#### 5. Workload Distribution (Plot 05)
- Boxplots by repository
- Compares workload intensity across projects
- Identifies high-intensity repositories

#### 6. After-Hours Patterns (Plot 06)
- Percentage of developer-weeks with after-hours activity
- Bar chart by repository
- Reveals work culture differences

### Insights Gained
- Commit patterns vary significantly across repositories
- Strong positive correlation between commits and churn
- After-hours coding is common (20-40% of weeks)
- Workload intensity is right-skewed (few very high values)

---

## Stage 4: Principal Component Analysis (PCA)

### Purpose
Reduce 15 correlated features to 5-7 uncorrelated principal components while retaining 90%+ variance.

### Process

#### 1. Feature Selection
15 features chosen for PCA:
- commits, lines_added, lines_deleted, files_changed
- total_churn, churn_ratio, avg_lines_per_commit, avg_files_per_commit
- risk_intensity, workload_intensity, refactor_signal
- hour_std, is_after_hours, file_spread, commit_density

#### 2. Standardization
```r
X_scaled = (X - mean(X)) / sd(X)
```
Centers each feature at 0 with unit variance. Essential because features have different scales.

#### 3. PCA Computation
- Computes covariance matrix of standardized features
- Extracts eigenvectors (principal component directions)
- Extracts eigenvalues (variance explained)
- Sorts components by eigenvalue (descending)

#### 4. Variance Analysis
- Calculates proportion of variance per component
- Cumulative variance to determine how many PCs needed
- Typical result: 5 PCs explain 88-92% of variance

### Interpretation

#### PC1 (30-40% variance)
**Interpretation**: Overall activity level
- High loadings: commits, total_churn, workload_intensity
- Separates active vs inactive developers

#### PC2 (15-25% variance)
**Interpretation**: Change granularity
- High loadings: avg_lines_per_commit, churn_ratio
- Separates small frequent commits vs large infrequent commits

#### PC3 (10-15% variance)
**Interpretation**: Temporal patterns
- High loadings: hour_std, is_after_hours
- Separates regular vs irregular work schedules

#### PC4-PC5 (5-10% variance each)
**Interpretation**: Structural impact and refactoring
- Loadings on file_spread, refactor_signal

### Visualizations

#### Scree Plot (Plot 07)
- Bar chart of variance per component
- Line showing cumulative variance
- Identifies "elbow" point

#### Biplot (Plot 08)
- Scatter plot of data points in PC1-PC2 space
- Colored by repository
- Ellipses show 68% confidence intervals
- Reveals repository clustering

#### Loading Plot (Plot 09)
- Arrows showing feature contributions to PC1-PC2
- Arrow length = importance
- Arrow direction = correlation with PCs

### Output
`data/pca_scores.csv` containing:
- All principal component scores (PC1-PC15)
- Original features (for interpretation)
- Metadata (repo, developer, week)

---

## Stage 5: Clustering Analysis

### Purpose
Discover natural groupings of developer behavior patterns using unsupervised learning.

### Optimal K Selection

#### Elbow Method
1. Run K-Means for k = 1 to 10
2. Calculate Within-Cluster Sum of Squares (WSS) for each k
3. Plot WSS vs k
4. Look for "elbow" where improvement diminishes
5. Typical elbow: k = 3-5

#### Silhouette Method
1. Run K-Means for k = 2 to 10
2. Calculate average silhouette score for each k
3. Higher score = better-defined clusters
4. Select k with maximum silhouette score
5. Typical best k: 3-4

#### Final K Selection
- Combines both methods
- Clamped to 3-6 for interpretability
- Typical choice: k = 4

### K-Means Clustering

#### Algorithm
1. **Initialize**: Randomly select k centroids
2. **Assign**: Assign each point to nearest centroid
3. **Update**: Recalculate centroids as mean of assigned points
4. **Repeat**: Steps 2-3 until convergence (centroids stop moving)

#### Parameters
- `centers = k` (determined above)
- `nstart = 50` (run 50 times with different initializations, keep best)
- `iter.max = 200` (maximum iterations)

#### Input
First 5 principal components (PC1-PC5) from PCA stage.

### Hierarchical Clustering

#### Algorithm
1. Start with each point as its own cluster
2. Repeatedly merge the two closest clusters
3. Continue until all points in one cluster
4. Cut tree at desired height to get k clusters

#### Parameters
- `method = "ward.D2"` (minimizes within-cluster variance)
- `distance = "euclidean"` (straight-line distance)

#### Sample Size
- Uses 400 random samples for computational efficiency
- Full dataset would be too slow for dendrogram visualization

### Cluster Interpretation

#### Method
1. Calculate mean of original features for each cluster
2. Compare cluster means to overall means
3. Identify distinguishing characteristics
4. Assign descriptive labels

#### Typical Clusters

**Cluster 1: High-Intensity Burst**
- High commits (>10/week)
- High workload_intensity (>500)
- High total_churn (>2000 lines)
- Interpretation: Sprint periods, feature development

**Cluster 2: Refactoring Activity**
- High refactor_signal (>0.45)
- High churn_ratio (>0.8)
- Moderate commits
- Interpretation: Code cleanup, technical debt reduction

**Cluster 3: Low-Activity Maintenance**
- Low commits (<3/week)
- Low total_churn (<500 lines)
- Low workload_intensity
- Interpretation: Bug fixes, minor updates, inactive periods

**Cluster 4: Normal Feature Development**
- Moderate all metrics
- Balanced churn_ratio (0.3-0.6)
- Regular work hours
- Interpretation: Steady feature development

### Validation

#### Silhouette Analysis
- Measures how well each point fits its cluster
- Score range: -1 (wrong cluster) to +1 (perfect fit)
- Average silhouette score: 0.3-0.5 (acceptable for behavioral data)

#### Visual Inspection
- Clusters should be visually separable in PC1-PC2 space
- Ellipses should have minimal overlap

### Output
- `data/clustered_data.csv`: All records with cluster labels
- `output/cluster_summary.csv`: Cluster profiles (means, counts, labels)

---

## Stage 6: OLAP Operations

### Purpose
Demonstrate data warehouse concepts through multi-dimensional analysis.

### Data Cube Structure

**Dimensions**:
- Time: week → month → quarter → year
- Repository: 5 repositories
- Developer: 40-80 unique developers
- Behavior: 3-4 cluster labels

**Measures**:
- total_commits, total_churn, active_devs
- avg_workload, avg_risk, avg_refactor_signal

### Operations

#### 1. Roll-Up (Aggregation)

**Week → Month**:
```r
group_by(repo, month) %>%
summarise(
  total_commits = sum(commits),
  total_churn = sum(total_churn),
  active_devs = n_distinct(developer)
)
```

**Month → Quarter**:
```r
group_by(repo, quarter) %>%
summarise(...)
```

**Purpose**: Identify long-term trends, reduce noise

**Output**: 
- `output/olap_rollup_monthly.csv`
- `output/olap_rollup_quarterly.csv`

#### 2. Drill-Down (Disaggregation)

**Quarter → Month → Week**:
```r
filter(repo == top_repo) %>%
group_by(week) %>%
summarise(...)
```

**Purpose**: Investigate specific time periods in detail

**Output**: `output/olap_drilldown_weekly.csv`

#### 3. Slice (Single Dimension Value)

**Example**: Django repository, Q1 2024
```r
filter(repo == "django", quarter == "2024 Q1")
```

**Purpose**: Focus on specific subset

**Output**: `output/olap_slice.csv`

#### 4. Dice (Multiple Dimension Values)

**Example**: Django & Flask, Q1 & Q2, High-intensity & Refactoring
```r
filter(
  repo %in% c("django", "flask"),
  quarter %in% c("2024 Q1", "2024 Q2"),
  behavior_label %in% c("High-intensity burst", "Refactoring activity")
)
```

**Purpose**: Complex multi-dimensional filtering

**Output**: `output/olap_dice.csv`

#### 5. Pivot (Rotation)

**Example**: Repository × Behavior matrix
```r
group_by(repo, behavior_label) %>%
summarise(avg_commits = mean(commits)) %>%
pivot_wider(names_from = behavior_label, values_from = avg_commits)
```

**Purpose**: Cross-tabulation for comparison

**Output**: `output/olap_pivot.csv`

### Visualizations

#### Roll-Up Plot (Plot 14)
- Stacked bar chart of monthly commits by repository
- Shows temporal trends and repository contributions

#### Drill-Down Plot (Plot 15)
- Weekly detail for top repository
- Bars = commits, line = normalized churn

#### Pivot Heatmap (Plot 16)
- Repository × Behavior matrix
- Color intensity = average workload
- Reveals which repos exhibit which behaviors

---

## Statistical Rigor

### Assumptions
- **Independence**: Developer-weeks treated as independent observations
- **Normality**: Not required (using non-parametric methods)
- **Stationarity**: Assumes behavior patterns are relatively stable over 6 months

### Limitations
- **Sample size**: 500-1000 records (moderate)
- **Temporal coverage**: 6 months (limited for long-term trends)
- **Repository diversity**: 5 repos (Python-focused)
- **Synthetic data**: Fallback may not capture all real-world nuances

### Validation
- **Cross-validation**: Not applicable (unsupervised learning)
- **Silhouette scores**: Validate cluster quality
- **Visual inspection**: Confirm patterns make sense
- **Domain knowledge**: Interpret clusters using software engineering expertise

---

## Computational Complexity

### Time Complexity
- **Data extraction**: O(n) where n = number of commits
- **Feature engineering**: O(m) where m = number of records
- **PCA**: O(p²m) where p = number of features
- **K-Means**: O(kmi) where i = iterations (typically 10-50)
- **Hierarchical**: O(m²log m) (reason for sampling)

### Space Complexity
- **Raw data**: ~100 KB
- **Cleaned data**: ~200 KB
- **PCA scores**: ~300 KB
- **Plots**: ~5 MB total

### Runtime
- **Total pipeline**: 30-60 seconds on modern hardware
- **Bottleneck**: Hierarchical clustering (if using full dataset)

---

## Reproducibility

### Ensured By
1. **Set seed**: `set.seed(42)` for random operations
2. **Version control**: All scripts in Git
3. **Package versions**: Auto-install ensures consistency
4. **Documented parameters**: All hyperparameters explicit in code

### To Reproduce
1. Clone repository
2. Run `Rscript R/00_run_all.R`
3. Compare outputs to provided results

---

## Extensions & Future Work

### Possible Enhancements
1. **More repositories**: Expand to 20-50 repos across languages
2. **Longer timeframe**: Analyze 1-2 years of data
3. **Supervised learning**: Predict developer burnout, code quality
4. **Real-time dashboard**: Live monitoring of developer patterns
5. **Network analysis**: Developer collaboration graphs
6. **Text mining**: Analyze commit messages for sentiment
7. **Anomaly detection**: Flag unusual behavior patterns
8. **Time series forecasting**: Predict future commit patterns
