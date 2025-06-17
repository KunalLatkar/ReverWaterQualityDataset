# Water Quality Dataset (India) – Gathering & Preprocessing

This repository contains a cleaned and structured dataset of river water quality indicators collected from monitoring stations across India from **2012 to 2023**. The data was gathered from the official Central Pollution Control Board (CPCB) portal: [https://cpcb.nic.in/nwmp-data-2023/](https://cpcb.nic.in/nwmp-data-2023/)

The focus of this repository is solely on **data cleaning, transformation, and preparation** 

---

## Dataset Overview

The dataset includes the following fields:

- **Station Code**
- **Monitoring Location**
- **State**
- **Temperature (°C)**: Min, Max
- **Dissolved Oxygen (mg/L)**: Min, Max
- **pH**: Min, Max
- **Conductivity (µmho/cm)**: Min, Max
- **Biological Oxygen Demand (BOD) (mg/L)**: Min, Max
- **Nitrate N (mg/L)**: Min, Max
- **Fecal Coliform (MPN/100ml)**: Min, Max
- **Total Coliform (MPN/100ml)**: Min, Max
- **Fecal Streptococci (MPN/100ml)**: Min, Max
- **Year**
- **Contaminated** (Binary target field derived from quality rules)

---

## Preprocessing Steps

The dataset was prepared through the following structured steps:

### 1. **Handling Categorical Missing Data**
- For the `"Monitoring Location"` column, missing entries were filled using the most common value in the dataset.
- In the `"State"` column, missing values were inferred based on previous and next row entries where possible, and replaced accordingly.

### 2. **Numeric Field Parsing and Cleaning**
- All measurement fields were explicitly converted to numeric values.
- Non-numeric entries were coerced into `NaN` and handled in the next step.

### 3. **Missing Value Imputation**
- For each numeric field, missing values were filled using the **mean of the corresponding state group** to preserve geographical context.
- The imputation was done column-by-column using a `groupby('State')` operation.

### 4. **Outlier Detection and Treatment**
- Outliers in numerical columns were identified using the **Interquartile Range (IQR) method**.
- Values falling outside the range of `Q1 - 1.5×IQR` and `Q3 + 1.5×IQR` were considered outliers.
- These outliers were replaced using the **median of the corresponding state group** to prevent distortion of means.

### 5. **Feature Normalization**
- Column names were standardized for consistency (e.g., `pH Min`, `Conductivity Max`, etc.).
- Units were verified to be consistent across all samples (e.g., µmho/cm, mg/L).

### 6. **Target Variable Creation – `Contaminated`**
A binary target column called **`Contaminated`** was created based on the following established environmental safety thresholds:

| Parameter                          | Safe Range / Limit                                                                                     |
|-----------------------------------|----------------------------------------------------------------------------------------------------------|
| **Dissolved Oxygen (mg/L)**        | ≥ 5.0                                                                                                   |
| **pH**                             | 6.5 to 8.5                                                                                               |
| **Conductivity (µmho/cm)**         | ≤ 1500 (based on CPCB limits)                                                                            |
| **BOD (mg/L)**                     | ≤ 3.0                                                                                                   |
| **Nitrate N (mg/L)**               | ≤ 2.0 (conservative range based on Indian surface water standards)                                      |
| **Fecal Coliform (MPN/100ml)**     | ≤ 2500                                                                                                  |
| **Total Coliform (MPN/100ml)**     | ≤ 500                                                                                                   |
| **Fecal Streptococci (MPN/100ml)** | ≤ 100                                                                                                   |

A water sample was labeled as **`Contaminated = 1`** if **more than 3 parameters** violated their respective safe thresholds.

---

## Files

| File Name                              | Description                                            |
|----------------------------------------|--------------------------------------------------------|
| `RiverWaterQualityOverYears.csv`       | Original extracted data (raw)                          |
| `RiverWaterQualityOverYears_PreprocessedData.csv` | Cleaned and processed dataset ready for analysis      |
| `CodeToPreprocessData.ipynb`           | Jupyter notebook detailing all preprocessing steps     |
| `README.md`                            | This documentation file                                |

---

## Purpose

Clean water is essential for life. By making water quality data accessible, consistent, and machine-readable, this project aims to support:
- Environmental research
- Public health surveillance
- Policy-making and regulation
- Education and data science training

---

## License

MIT License

Copyright (c) 2024 KunalLatkar

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS ARE LIABLE FOR ANY CLAIM, DAMAGES, OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT, OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OF OTHER DEALINGS IN THE
SOFTWARE.
