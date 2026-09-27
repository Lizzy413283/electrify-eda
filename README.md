# The $5,000 Tipping Point
When does the growth stop mattering? An EDA on the $5K threshold to universal energy access.

# Project Overview
Exploratory Data Analysis of global electricity access and economic indicators to identify the GDP per capita threshold where rural electrification consistently surpasses 80%. Uses global dataset spanning 2000-2016 across 194 countries.

# Key Findings & Executive Summary

| Metric | Value |
| Tipping Point | ~$5,000 GDP per capita (log10 ≈ 3.7) |
| Log GDP vs Rural Access (Pearson) | r = 0.72 |
| Low Income & Energy Poor | 45 countries (23.2%) |
| Transition & Emerging Economies | 81 countries (41.8%) |
| High Income & Universal Grid | 68 countries (35.1%) |
| 2030 Forecast (Low Income) | ~30% rural access |
| SDG-7 Deficit (Low Income) | 70 percentage points |

Executive Summary:
 A strong log-linear relationship exists between GDP per capita and rural electricity access (r=0.72). The $5,000 GDP/capita threshold marks a clear tipping point: above it, rural access consistently exceeds 80%. However, 45 countries (23.2%) remain trapped in energy poverty (<$5K GDP, <50% rural access). Under status-quo trends, this group reaches only ~30% access by 2030 — a 70-point deficit against the UN SDG-7 universal access target.

## Dataset & Methodology

### Dataset
Source: `global_electricity_access_economic_indicators.csv` (external path)
Period: 2000-2016
Countries: 194
Key Columns:
- `country`, `date`
- `GDP_Per_Capita_Current_USD`
- `Electricity_Access_Urban_Percentage`, `Electricity_Access_Rural_Percentage`
- `Total_Population`, `Total_Electricity_Output_GWh`
- `Population_Female_Percentage`, `Population_Male_Percentage`
- `meter_id`, `source_url`, `notes` (dropped)

### Methodology

#1. Data Loading & Inspection
- Load CSV, display sample rows, check structure with `df.info()`

#2. Memory Optimization
- `country` → `category` dtype
- `date` → `int16`
- Percentages & GDP → `float32`
- Totals (`Total_Population`, `Total_Electricity_Output_GWh`) → `float64`
- Verified reduction via `memory_usage(deep=True)`

#3. Missing Value Analysis & Imputation
- Calculated missing % per column
- Skew-aware imputation for 7 numerical columns:
  - |skew| > 0.5 → median
  - Else → mean
- Columns: GDP per capita, Total Population, Female/Male %, Urban/Rural Access %, Electricity Output

#4. Urban-Rural Disparity Analysis
- Created `urban_rural_gap` = Urban Access - Rural Access
- Latest record per country (most recent year)
- Top 10 countries by gap magnitude

#5. Statistical Correlation Analysis
- Created `log_gdp` = log10(GDP_Per_Capita_Current_USD)
- Pearson (raw GDP vs Rural Access)
- Pearson (log GDP vs Rural Access) — stronger fit
- Spearman rank correlation
- Annotated heatmap of 6 variables

#6. Tipping Point Evaluation ($5,000)
- Split: Below $5K vs Above $5K
- Compared mean/median rural access
- Quadrant Analysis (4 categories):
  - Low Income (<$5k) & Low Access (<80%)
  - Low Income (<$5k) & High Access (>=80%) [OVERPERFORMERS]
  - High Income (>=$5k) & Low Access (<80%) [UNDERPERFORMERS]
  - High Income (>=$5k) & High Access (>=80%)
- Identified outlier countries in overperformer/underperformer quadrants

#7. Country Profiling
- Latest complete record per country
- #3-Profile Classification:
  - Low Income & Energy Poor: GDP < $5K AND Rural Access < 50%
  - High Income & Universal Grid: GDP >= $10K AND Rural Access >= 90%
  - Transition & Emerging Economies: all others
- Income Quartiles: qcut GDP into 4 bins

#8. SDG-7 Forecast to 2030
- Aggregated mean rural access per profile per year
- OLS linear trend (`np.polyfit`) per profile
- Projected 2016-2030 (clipped 0-100%)
- Reference: UN SDG-7 target (100%)

## Visual Analysis & Key Charts

| 1 | Top 10 Urban-Rural Gap Bar Chart | Horizontal bars (Reds_r), data labels, latest year per country |
| 2 | Urban vs Rural Access Boxplots | Side-by-side (skyblue/salmon), distribution comparison |
| 3 | Correlation Heatmap | 6 variables, annotated, coolwarm colormap |
| 4 | Log GDP vs Rural Access Scatter | Teal points, $5K line (crimson dashed), 80% line (orange dotted) |
| 5 | Profile Distribution by Income Quartile | Stacked horizontal bar (Blues_r), normalized % per quartile |
| 6 | Global Profile Breakdown Donut | 3 profiles, custom palette, center total (194) |
| 7 | Rural Access Forecast to 2030 | Time series: solid (historical 2000-2016), dashed (OLS 2016-2030) |

## How to Run the Code

### Prerequisites
- Python 3.10+
- `uv` (recommended) or `pip`

### Installation
```bash
# Using uv (faster, reproducible)
uv sync

# Or pip
pip install -r requirements.txt
```

### Run Analysis
```bash
# Start Jupyter
uv run jupyter lab

# Open notebook
notebooks/01_electricity_access_eda.ipynb

# Or convert to HTML report
uv run jupyter nbconvert --to html notebooks/01_electricity_access_eda.ipynb --output-dir reports/
```

### Data Setup
Place the source dataset at:
```
data/raw/global_electricity_access_economic_indicators.csv
```
Required columns listed in Dataset section above.

## Future Improvements
- Extend analysis to incluse regional energy infrastructure metrics.
- Build predictive machine learning models to forecast rural electrification timelines.
- Export clean summaries into automated PDF reports.

## License
MIT License — Free to use, modify, distribute.

## Author
Lindsay Ivy Opiyo
LinkedIn: 
Github:https://github.com/Lizzy413283