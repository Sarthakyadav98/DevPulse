# Technical Terms Glossary

## Data Mining Terms

### **Data Mining**
The process of discovering patterns, correlations, and insights from large datasets using statistical, mathematical, and computational techniques.

### **Feature Engineering**
Creating new variables (features) from raw data that better represent underlying patterns and improve analysis quality. Example: Creating `churn_ratio` from `lines_added` and `lines_deleted`.

### **Dimensionality Reduction**
Techniques to reduce the number of variables while preserving important information. Helps with visualization, computation speed, and avoiding the "curse of dimensionality."

### **Unsupervised Learning**
Machine learning approach where the algorithm finds patterns without labeled training data. Clustering is a common unsupervised technique.

---

## Principal Component Analysis (PCA)

### **PCA (Principal Component Analysis)**
A dimensionality reduction technique that transforms correlated variables into a smaller set of uncorrelated variables called principal components, ordered by variance explained.

### **Principal Component (PC)**
A new variable created as a linear combination of original features. PC1 captures the most variance, PC2 the second most, etc.

### **Eigenvalue**
Represents the amount of variance explained by each principal component. Larger eigenvalues indicate more important components.

### **Loading**
The weight/contribution of each original feature to a principal component. High absolute loadings indicate strong influence.

### **Scree Plot**
A graph showing variance explained by each principal component, used to determine how many components to retain (look for the "elbow").

### **Biplot**
A visualization showing both data points (scores) and feature vectors (loadings) in principal component space.

### **Variance Explained**
The proportion of total dataset variance captured by principal components. Typically aim for 80-90% cumulative variance.

---

## Clustering

### **Clustering**
Grouping similar data points together based on their features. Points in the same cluster are more similar to each other than to points in other clusters.

### **K-Means Clustering**
An algorithm that partitions data into K clusters by iteratively assigning points to the nearest cluster center (centroid) and updating centroids.

### **Hierarchical Clustering**
Builds a tree (dendrogram) of clusters by either merging small clusters (agglomerative) or splitting large ones (divisive).

### **Centroid**
The center point of a cluster, calculated as the mean of all points in that cluster.

### **Within-Cluster Sum of Squares (WSS)**
Measures compactness of clusters. Lower WSS means tighter, more cohesive clusters.

### **Elbow Method**
Technique to find optimal number of clusters (K) by plotting WSS vs K and looking for the "elbow" where improvement diminishes.

### **Silhouette Score**
Measures how well each point fits its cluster (-1 to +1). Higher scores indicate better-defined clusters. Average silhouette score evaluates overall clustering quality.

### **Dendrogram**
Tree diagram showing hierarchical relationships between clusters. Height indicates dissimilarity between merged clusters.

### **Ward's Method**
A hierarchical clustering approach that minimizes within-cluster variance at each merge step.

---

## OLAP (Online Analytical Processing)

### **OLAP**
Technology for multi-dimensional analysis of business data, enabling complex queries and aggregations across different dimensions (time, location, product, etc.).

### **Data Cube**
Multi-dimensional representation of data where each dimension represents a categorical variable (e.g., time, repository, developer).

### **Dimension**
A categorical attribute used to organize data (e.g., time, repository, behavior type).

### **Measure**
A numeric value being analyzed (e.g., commits, lines_added, workload_intensity).

### **Roll-Up (Aggregation)**
Moving from detailed data to summary data by climbing up a hierarchy. Example: Week → Month → Quarter → Year.

### **Drill-Down**
Moving from summary to detailed data by descending a hierarchy. Example: Year → Quarter → Month → Week.

### **Slice**
Selecting a single value from one dimension, creating a sub-cube. Example: "Show only Django repository data."

### **Dice**
Selecting specific values from multiple dimensions, creating a smaller sub-cube. Example: "Show Django and Flask, in Q1 and Q2, for high-intensity behavior."

### **Pivot (Rotation)**
Rotating the data cube to view it from different perspectives. Often creates cross-tabulation tables (e.g., Repository × Behavior matrix).

---

## Statistical Terms

### **Correlation**
Measures linear relationship between two variables (-1 to +1). Positive correlation means variables increase together; negative means one increases as the other decreases.

### **Outlier**
A data point significantly different from other observations. Can indicate errors or genuine extreme values.

### **IQR (Interquartile Range)**
The range between the 25th percentile (Q1) and 75th percentile (Q3). Used to identify outliers: values beyond Q1 - 1.5×IQR or Q3 + 1.5×IQR.

### **Normalization**
Scaling features to a common range (e.g., 0-1) to enable fair comparison and prevent features with large values from dominating analysis.

### **Standard Deviation**
Measures spread of data around the mean. Higher values indicate more variability.

### **Quantile/Percentile**
Values that divide data into equal-sized groups. The 25th percentile (Q1) means 25% of data falls below that value.

---

## Project-Specific Metrics

### **Churn**
Total lines of code changed (added + deleted). High churn indicates significant code modifications.

### **Churn Ratio**
Proportion of deletions to additions (lines_deleted / lines_added). High ratio suggests refactoring or code removal.

### **Workload Intensity**
Composite metric: commits × log(total_churn). Captures both frequency and magnitude of work.

### **Risk Intensity**
Metric combining code volatility and temporal patterns: (total_churn / files_changed) × (hour_std / 5). Higher values suggest riskier changes.

### **Refactor Signal**
Proportion of deletions in total churn (lines_deleted / total_churn). Values > 0.4 suggest refactoring activity.

### **After-Hours Coding**
Binary indicator for commits outside standard work hours (before 9am or after 6pm). May indicate deadline pressure or flexible schedules.

### **File Spread**
log(files_changed + 1). Measures how widely changes are distributed across the codebase.

---

## Data Warehouse Terms

### **ETL (Extract, Transform, Load)**
Process of extracting data from sources, transforming it into usable format, and loading into a data warehouse.

### **Data Warehouse**
Centralized repository storing integrated data from multiple sources, optimized for analysis and reporting.

### **Fact Table**
Central table in a data warehouse containing measurable events (facts) and foreign keys to dimension tables.

### **Dimension Table**
Tables containing descriptive attributes (dimensions) that provide context to facts. Examples: time, location, product.

### **Star Schema**
Data warehouse design with a central fact table connected to dimension tables, resembling a star shape.

---

## Visualization Terms

### **Heatmap**
Color-coded matrix visualization where color intensity represents values. Used for correlation matrices and pivot tables.

### **Boxplot**
Shows distribution through quartiles, median, and outliers. Box spans Q1 to Q3, line shows median, whiskers extend to min/max (excluding outliers).

### **Scatter Plot**
Displays relationship between two continuous variables as points on a 2D plane.

### **Time Series Plot**
Line graph showing how values change over time.

### **Histogram**
Bar chart showing frequency distribution of a continuous variable by dividing it into bins.

### **Faceting**
Creating multiple small plots (facets) for different subgroups of data, enabling comparison across categories.

---

## Algorithm Terms

### **Iteration**
One complete pass through an algorithm's main loop. K-Means iterates by reassigning points and updating centroids until convergence.

### **Convergence**
When an iterative algorithm reaches a stable state where further iterations produce minimal change.

### **Seed**
Initial value for random number generation. Setting a seed ensures reproducible results.

### **Distance Metric**
Method for measuring similarity/dissimilarity between data points. Common metrics: Euclidean distance, Manhattan distance.

### **Euclidean Distance**
Straight-line distance between two points in multi-dimensional space: √(Σ(xi - yi)²)

---

## R Programming Terms

### **Data Frame**
R's primary data structure for tabular data, similar to a spreadsheet or database table.

### **Pipe Operator (%>%)**
Chains operations together, passing output of one function as input to the next. Makes code more readable.

### **Factor**
R's data type for categorical variables with fixed levels.

### **Aggregate**
Combining multiple rows into summary statistics (sum, mean, count, etc.) based on grouping variables.

---

## GitHub/Git Terms

### **Commit**
A snapshot of code changes saved to version control. Contains author, timestamp, and modified files.

### **Repository (Repo)**
A project's complete codebase and version history stored in Git.

### **Developer**
Person who makes commits to a repository.

### **API (Application Programming Interface)**
Interface allowing programs to interact with services. GitHub API provides programmatic access to repository data.

---

## Key Takeaways for Presentation

**Most Important Terms to Know**:
1. **PCA**: Reduces dimensions while keeping important patterns
2. **K-Means**: Groups similar developers/behaviors together
3. **OLAP**: Analyzes data from multiple perspectives (time, repo, behavior)
4. **Churn**: How much code changed
5. **Clustering**: Finding natural groups in data
6. **Feature Engineering**: Creating meaningful metrics from raw data

**Simple Explanations**:
- **PCA**: "Compress 15 features into 5 components without losing information"
- **Clustering**: "Automatically discover developer behavior types"
- **OLAP**: "Slice and dice data like a pivot table on steroids"
- **Churn**: "How much code is being rewritten"
