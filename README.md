# EIA Clustering Project - Electrical Grid Fault Detection

## Overview
This project implements an unsupervised machine learning approach to detect potential faults in electrical grids by clustering consumption patterns from 401 electricity meters belonging to Colombian energy customers (estratos 3-4). The analysis follows the **ASUM-DM (Analytics Solutions Unified Method for Data Mining)** methodology to identify consumption behavior groups that may indicate abnormal meter operation or grid issues.

## Project Structure
```
Grouping_project/
├── data/                          # Input data directory
│   └── medidores_EIA.csv          # Raw meter readings (ID, FECHA, KWH_IMPORT)
├── results/                       # Analysis outputs
│   ├── data_with_clusters.csv     # Dataset with cluster assignments
│   ├── cluster_statistics.csv     # Summary statistics per cluster
│   └── InformeFinal.pdf           # Final report
├── EIA_agrupamiento.ipynb         # Main analysis notebook
├── requirements.txt               # Python dependencies
└── README.md                      # Project documentation
```

## Installation

### Prerequisites
- Python 3.8 or higher
- Virtual environment tool (venv, conda, etc.)

### Setup Instructions
1. Clone or download this repository to your local machine

2. Create and activate a virtual environment:
   ```powershell
   python -m venv .venv
   .venv\Scripts\Activate.ps1
   ```

3. Install required dependencies:
   ```powershell
   pip install -r requirements.txt
   ```

4. Ensure your input data is placed in the `data/` folder with the expected format:
   - **ID**: Meter identifier
   - **FECHA**: Date of reading (YYYY-MM-DD format)
   - **KWH_IMPORT**: Cumulative energy consumption in kWh

## Usage

### Running the Analysis
1. Open `EIA_agrupamiento.ipynb` in Jupyter Notebook or JupyterLab:
   ```powershell
   jupyter notebook EIA_agrupamiento.ipynb
   ```

2. Execute cells sequentially from top to bottom. The notebook is organized into the following sections:
   - **Data Loading & Exploration**: Import dataset, inspect structure and data types
   - **Data Preprocessing**: Calculate CONSUMO_NETO, handle meter resets, remove faulty meters
   - **Feature Engineering**: Create cyclical encodings (MES_sin, MES_cos, DIA_sin, DIA_cos) and meter statistics
   - **Exploratory Data Analysis**: Visualize distributions, correlations, and temporal patterns
   - **Clustering Models**: Train and evaluate 6 clustering algorithms
   - **Model Selection**: Compare silhouette scores and select optimal model
   - **Cluster Analysis**: Interpret clusters using quantile-based classification
   - **Visualization**: Generate weekly consumption patterns and comparison charts

3. Review results in the `results/` folder after execution

### Expected Outputs
- **data_with_clusters.csv**: Original dataset with added cluster assignment column
- **cluster_statistics.csv**: Aggregated metrics per cluster (mean, std, count)
- **Visualizations**: Histograms, correlation heatmaps, box plots, bar charts, time series plots


## Methodology

### Feature Engineering
The analysis uses engineered features to capture consumption patterns:
- **CONSUMO_NETO**: Difference between consecutive cumulative readings per meter
- **Cyclical Encodings**: Sin/cos transformations for MES (month) and DÍA (day) to preserve temporal proximity
- **Meter Statistics**: METER_AVG_CONSUMO, METER_STD_CONSUMO for context
- **CONSUMO_DEVIATION**: Normalized deviation from meter-specific average consumption

### Clustering Approach
Six clustering algorithms were evaluated:
1. **KMeans** (k=5) - Selected as best model ✓
2. **KMeans** (k=12) - Alternative granularity
3. **MiniBatchKMeans** (k=4) - Memory-efficient variant
4. **BIRCH** (k=4) - Hierarchical method optimized via threshold tuning
5. **DBSCAN** - Density-based with hyperparameter grid search
6. **Gaussian Mixture Model** (n=15) - Probabilistic clustering

**Model Selection Criteria**: KMeans with k=5 was selected based on:
- Highest silhouette score among tested models
- Business interpretability (manageable number of groups)
- Fairly clear separation between consumption patterns

### Cluster Interpretation
Clusters are classified using quantile-based thresholds:
- **Consumption Level**: BAJO (<33rd percentile), MEDIO (33rd-67th), ALTO (>67th)
- **Variability**: ESTABLE (<median std), VARIABLE (≥median std)

This produces interpretable labels like "CONSUMO MEDIO - VARIABILIDAD ALTA" for each cluster.

## Key Features

### Data Preprocessing
- Automatic conversion of date columns to datetime objects
- Calculation of net consumption from cumulative readings
- Detection and handling of meter resets (saturation at 999,999 kWh)
- Removal of faulty all-zero consumption meters

### Memory Optimization
- Sample-based visualization for hierarchical clustering (1,500-2,000 rows)
- Efficient grid search strategies for DBSCAN hyperparameter tuning
- MiniBatchKMeans for scalable clustering on large datasets

### Visualization
- Dynamic histogram generation handling variable numbers of numeric features
- Weekly consumption patterns for representative meters per cluster
- Box plots and bar charts for cluster comparison
- Correlation heatmaps identifying multicollinearity

## Dependencies
Core libraries used in this project:
- **pandas**: Data manipulation and analysis
- **numpy**: Numerical computing
- **scikit-learn**: Machine learning algorithms and preprocessing
- **matplotlib & seaborn**: Data visualization
- **scipy**: Scientific computing and hierarchical clustering

See `requirements.txt` for complete dependency list with versions.

## Results Interpretation

### Cluster Characteristics
The final KMeans k=5 model produces clusters with distinct consumption profiles:
- Each cluster represents a unique combination of consumption level and variability
- Weekly patterns reveal consumption behavior differences (weekday vs. weekend usage)
- Quantile-based classification enables relative comparison across seasonal changes

### Use Cases
- **Fault Detection**: Identify meters with abnormal consumption patterns
- **Grid Monitoring**: Group meters by behavior for targeted inspection
- **Resource Planning**: Understand customer consumption segments for infrastructure investment

## Troubleshooting

### Common Issues
1. **Memory Errors**: Reduce sample sizes for hierarchical clustering or use MiniBatchKMeans
2. **Missing Data**: Ensure all required columns (ID, FECHA, KWH_IMPORT) are present
3. **Import Errors**: Verify all dependencies installed via `pip install -r requirements.txt`

### Contact
For questions or issues related to this analysis, contact the author at santiago.estrada6@eia.edu.co.

---
**Project Type**: Electrical Grid Fault Detection  
**Methodology**: ASUM-DM (Analytics Solutions Unified Method for Data Mining)  
**Target Audience**: Colombian energy company, estratos 3-4 customers  
**Dataset Size**: 401 meters, ~140,000 observations
