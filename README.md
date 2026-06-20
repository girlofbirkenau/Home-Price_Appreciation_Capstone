# Predicting Long-Run Home Price Appreciation Across U.S. Housing Markets

**WGU Data Analytics Capstone: D502**  
**Author:** Jessica Wales

---

## Research Question

Which metropolitan and macroeconomic characteristics are most predictive of long-run home price appreciation across U.S. housing markets?

---

## Project Overview

This project uses a supervised machine learning pipeline to identify the metropolitan-area and economic factors that best predict long-run residential home price appreciation across the 100 largest U.S. metropolitan statistical areas (MSAs). Using quarterly price index data spanning from 1991 to present alongside annual demographic and economic indicators collected from 2011 onward, the project trains and evaluates regression models to uncover which characteristics, including population growth, unemployment rate, poverty rate, median income, educational attainment, vacancy rate, homeownership rate, and the 30-year fixed mortgage rate, are most strongly associated with home price appreciation.

---

## Repository Structure

```
Home-Price_Appreciation_Capstone/
├── README.md
├── .gitignore
├── requirements.txt
├── data/
│   ├── raw/                        # Source data
│   ├── processed/                  # Cleaned, merged panel dataset
│   └── figures/                    # Saved chart outputs from EDA and modeling
├── 01_data_collection.ipynb        # API calls and raw data collection
├── 02_data_processing.ipynb        # Cleaning, merging, feature processing
├── 03_eda.ipynb                    # Exploratory data analysis and visualizations
└── 04_modeling.ipynb               # Regression models, evaluation, and results
```

---

## Data Sources

All data is publicly available and free to download. Instructions for obtaining each source are below.

### 1. FHFA House Price Index: 100 Largest MSAs
- **URL:** https://www.fhfa.gov/data/hpi/datasets?tab=quarterly-data
- **Section:** Purchase-Only Indexes -> *100 Largest Metropolitan Statistical Areas*
- **Format:** XLSX
- **Save to:** `data/raw/hpi_po_metro.xlsx`

### 2. Census ACS: Metropolitan Demographic and Economic Indicators (2011–2024)
- Pulled seven detailed tables via the U.S. Census Bureau API (ACS 1-Year Estimates):
  - B01003: Total population
  - B23025: Labor force and unemployment
  - B17001: Poverty status
  - B19013: Median household income
  - B15003: Educational attainment
  - B25002: Housing occupancy and vacancy
  - B25003: Housing tenure and homeownership
- A free API key is required: https://api.census.gov/data/key_signup.html
- Data is collected automatically by `01_data_collection.ipynb`
- **Note:** ACS 1-year detailed tables are only available beginning in 2011; no 2020 estimates were published due to COVID-related data collection disruptions, so 2020 is excluded from the panel.

### 3. FRED: 30-Year Fixed Mortgage Rate
- **Series:** `MORTGAGE30US` (Freddie Mac Primary Mortgage Market Survey)
- Pulled via the FRED API
- A free API key is required: https://fred.stlouisfed.org/docs/api/api_key.html
- Data is collected automatically by `01_data_collection.ipynb`

---

## Setup and Usage

### 1. Clone the repository
```bash
git clone https://github.com/girlofbirkenau/Home-Price_Appreciation_Capstone.git
cd Home-Price_Appreciation_Capstone
```

### 2. Install dependencies
```bash
pip install -r requirements.txt
```

### 3. Configure API keys
Create a file named `.env` in the project root with the following contents:
```
CENSUS_API_KEY=your_census_key_here
FRED_API_KEY=your_fred_key_here
```
This file is excluded from Git and will never be pushed to the repository.

### 4. Download FHFA data
Download the FHFA HPI file manually (see Data Sources above) and place it at `data/raw/hpi_po_metro.xlsx`.

### 5. Run the notebooks in order
Open the project in VS Code and run each notebook sequentially:
1. `01_data_collection.ipynb` - fetches and saves all raw data
2. `02_data_processing.ipynb` - cleans and merges into a panel dataset
3. `03_eda.ipynb` - exploratory analysis and visualizations
4. `04_modeling.ipynb` - model training, evaluation, and results

---

## Technologies

- **Python 3.11+**
- **pandas**: data manipulation and panel dataset construction
- **requests**: Census and FRED API calls
- **scikit-learn**: regression modeling and evaluation pipeline
- **scipy**: Pearson correlation hypothesis testing
- **matplotlib / seaborn**: visualizations
- **openpyxl**: reading FHFA Excel files
- **python-dotenv**: secure API key management
- **jupyter**: interactive notebook environment

---

## Key Findings

After cleaning and merging all three data sources, the final panel dataset contained 1,178 observations across 99 of the 100 largest U.S. metropolitan areas, spanning 2012–2024 (2020 excluded due to a gap in ACS data availability).

A Pearson correlation test found a statistically significant positive relationship between population growth rate and home price appreciation (r = 0.066, p = 0.023), though the practical effect size was small. The 30-year fixed mortgage rate showed the strongest individual relationship with appreciation of any predictor tested (r = -0.290, p < 0.001).

Two regression models were trained to predict annual home price appreciation:

| Model | R² (test) | RMSE |
|-------|-----------|------|
| Linear Regression (baseline) | 0.11 | 5.44 pp |
| Random Forest (primary) | 0.58 | 3.75 pp |

The random forest substantially outperformed the linear baseline, confirming that the relationships between metropolitan/economic characteristics and appreciation are largely non-linear. Feature importance rankings identified the mortgage rate as the dominant predictor (35% of total importance), with population growth rate, unemployment rate, and vacancy rate each contributing roughly 11% individually. The remaining predictors (median income, educational attainment, homeownership rate, and poverty rate) each contributed under 8%.

