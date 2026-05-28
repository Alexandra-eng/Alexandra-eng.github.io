# Energy Data Pipeline

**A production-ready ETL pipeline demonstrating data extraction, transformation, and loading capabilities for energy sector datasets.**

---

## Project Overview

This project automates the retrieval and processing of energy department datasets, converting manual data collection processes into a scalable, reusable pipeline. The pipeline ingests electricity sales and capability data from multiple formats, applies business logic transformations, and outputs clean, analysis-ready data.

**Use Case:** Extract electricity sales metrics filtered by residential and transportation sectors, normalize nested JSON data, and prepare datasets for downstream analytics and reporting.

---

## Architecture

### ETL Pipeline Design

```
Raw Data (CSV, JSON)
    ↓
[Extract] - Format validation and parsing
    ↓
[Transform] - Business logic: filtering, feature engineering
    ↓
[Load] - Serialization to CSV/Parquet
    ↓
Clean Data (CSV, Parquet)
```

### Key Features

- **Multi-format extraction**: Handles CSV, Parquet, and nested JSON data sources
- **Data validation**: Type checking and null handling with null value constraints
- **Format flexibility**: Outputs to both CSV and compressed Parquet formats
- **Modular design**: Reusable functions enabling easy integration into larger pipelines

---

## Technical Stack

- **Language**: Python 3.x
- **Core Libraries**: 
  - `pandas` - Data manipulation and transformation
  - `json` - Nested data parsing and flattening
- **Data Formats**: CSV, JSON, Parquet (with gzip compression)

---

## Pipeline Functions

### `extract_tabular_data(file_path: str)`
Extracts structured data from CSV or Parquet files using pandas with automatic format detection.
- **Input**: File path to CSV or Parquet file
- **Output**: `pandas.DataFrame`
- **Error Handling**: Raises exception for unsupported file types

### `extract_json_data(file_path: str)`
Extracts and flattens nested JSON data, converting hierarchical structures into tabular format using `pd.json_normalize()`.
- **Input**: File path to JSON file
- **Output**: Flattened `pandas.DataFrame`

### `transform_electricity_sales_data(raw_data: pd.DataFrame)`
Applies business logic to electricity sales data:
- Removes null values in the `price` column
- Filters to include only residential and transportation sectors
- Extracts year and month from `period` field
- Returns subset of relevant columns for analysis

| Input Column | Output Column | Transformation |
|---|---|---|
| `period` | `year`, `month` | String slicing (last 2 chars, first 4 chars) |
| `sectorName` | - | Filtered to ['residential', 'transportation'] |
| `price` | `price` | Null values dropped |

### `load(dataframe: pd.DataFrame, file_path: str)`
Persists transformed data to disk in CSV or Parquet format with automatic compression for Parquet.
- **Parquet**: Uses gzip compression for optimized storage
- **CSV**: Plain text format for accessibility

---

## Usage

```python
from pipeline import extract_json_data, extract_tabular_data, transform_electricity_sales_data, load

# Extract data from multiple formats
raw_electricity_capability_df = extract_json_data("electricity_capability_nested.json")
raw_electricity_sales_df = extract_tabular_data("electricity_sales.csv")

# Transform electricity sales data
cleaned_electricity_sales_df = transform_electricity_sales_data(raw_electricity_sales_df)

# Load processed data
load(raw_electricity_capability_df, "loaded__electricity_capability.parquet")
load(cleaned_electricity_sales_df, "loaded__electricity_sales.csv")
```

---

## Files

- `pipeline.ipynb` - Complete ETL pipeline with extraction, transformation, and loading functions
- `electricity_sales.csv` - Raw electricity sales dataset
- `electricity_capability_nested.json` - Nested electricity capability data
- `loaded__electricity_sales.csv` - Processed electricity sales data (output)