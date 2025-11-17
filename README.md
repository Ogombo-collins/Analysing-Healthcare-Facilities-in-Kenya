# Kenya’s Healthcare Facilities Analysis (2025)

This project provides a detailed analysis of the distribution, capacity, and accessibility of health facilities across Kenya. Using publicly available datasets, including national health facility registries and 2019 county population census data. The notebook explores disparities, infrastructure gaps, and county-level differences affecting healthcare access.

The goal of the project: Offer **data-driven insights** that can support policymakers, health economists, county planners, development partners, and researchers working in Kenya’s health sector.

---

## 📊 Key Objectives

1. **Understand geographic/healthcare services distribution of healthcare facilities**
   - By county 
   - By facility type (dispensary, health centre, clinic, etc.)  
   - By ownership (public, private, etc)

2. **Assess healthcare access relative to population**
   - Facilities per 10,000 residents  
   - Most underserved counties  
   - Counties with over-concentration or facility gaps

3. **Identify infrastructure disparities across counties**
   - Urban vs rural mismatches  
   - Population density impact  
   - Service availability gaps

4. **Provide practical insights for resource allocation and planning**

---

## 🧵 Data Sources

1. **Kenya Health Facilities Registry**  
   - Retrieved from OpenAfrica’s Health Facilities in Kenya dataset. It's updated monthly. 
   - Includes facility type, ownership, geolocation, and operational status.
   - Access data here: <a href="https://open.africa/dataset/health-facilities-in-kenya" target="_blank" rel="noopener">Health facilities in Kenya </a>


2. **Kenya Population & Housing Census (2019)**  
   - Contains population counts per county. Used to calculate population-adjusted metrics such as facility ratios.
   - Access data here:  <a href="https://www.knbs.or.ke/wp-content/uploads/2023/09/2019-Kenya-population-and-Housing-Census-Volume-1-Population-By-County-And-Sub-County.pdf" target="_blank" rel="noopener">Counties population</a>

---

## 🛠️ Methodology & Analysis

### 1. Initial Data Cleaning & Transformation

These steps were applied to the raw health facilities dataset (`df`):

- **Handle Missing Capacity Data:**
  - Missing values in **`Beds`** and **`Cots`** were filled with **0**.
  - Both columns were converted to integer type.

- **Feature Engineering: `Total_Capacity`**
  - Created a new column **`Total_Capacity`** by summing cleaned `Beds` and `Cots` for each facility.

- **Standardise Operational Columns (Binarization):**
  - **`Open 24 Hours`** → **`Open_24_Hours`** (1 for 'Y', 0 otherwise)
  - **`Open Weekends`** → **`Open_Weekends`** (1 for 'Y', 0 otherwise)
  - **`Operational Status`** → **`Is_Operational`** (1 for 'Operational', 0 otherwise)

- **Standardise Service Columns (Binarization):**
  - Converted the following columns to binary (Y → 1, else → 0):
    - `ART`, `C-IMCI`, `FP`, `HBC`, `IPD`

- **Drop Unnecessary Columns:**
  - Created a new DataFrame (`df_cleaned`) by dropping 19 columns:
    - The original operational columns were replaced by binary versions.
    - Seventeen empty service columns, such as:
      - `ANC`, `BEOC`, `BLOOD`, `CAES SEC`, `OPD`, `PMTCT`, etc.

---

### 2. Data Filtering

- **Remove Non-Operational Facilities:**
  - Filtered `df_cleaned` to keep only rows where **`Is_Operational = 1`**.
  - Removed **954** non-operational facilities.
  - Final dataset for analysis: **9,551 operational facilities**.

---

### 3. Data Augmentation, Aggregation & Merging

To enable county-level insights, cleaned facility data was combined with population data.

#### Create Population Dataset
- Constructed a new DataFrame (`df_population`) containing 2019 KNBS population figures for all 47 counties.

#### Standardize County Names
Cleaned the `County` column by:
1. Converting names to title case.
2. Removing whitespace.
3. Fixing inconsistencies:
   - `Elgeyo/Marakwet` → `Elgeyo Marakwet`
   - `Nairobi City` → `Nairobi`
   - `Muranga` → `Murang'a`

#### Aggregate Facility Data by County
Grouped the facility-level dataset to create **`df_facility_agg`**, containing:
- `Facility_Count`
- `Total_Capacity`
- `ART_Facility_Count`
- `IPD_Facility_Count`
- `FP_Facility_Count`
- `Open_24h_Count`

#### Merge Datasets
Merged **`df_facility_agg`** with **`df_population`** on `County` to form **`df_merged`**.

#### Feature Engineering: Density Metrics
Added per-capita metrics:
- **`Facilities_Per_100k_People`**
- **`Beds_Per_1000_People`**
- **`ART_Clinics_Per_100k_People`**

---

## **Exploratory Data Analysis**
- Facility distribution by:
  - County  
  - Operations (Type, 24-hrs status, etc)  
  - Ownership type
- Population-adjusted metrics  
- Ranking of counties by service coverage  

## **Visualizations**
- Facility distribution bar charts  
- Population vs facility ratio charts  
- County-level comparisons on capacity, services, etc  
- Highlighting counties with extreme values
- Top 5 Facility Owners, and Types

---
# **Insights & Interpretation**
Key Insights include:

### 🏥 Facility Distribution and Operations

- **Total Facilities:**  
  After cleaning and filtering for operational status, the analysis focused on **9,551 active health facilities** across Kenya.

- **Most Common Facility Types:**  
  The healthcare system is dominated by primary care facilities:  
  - **Dispensaries:** 45.9% of all facilities  
  - **Medical Clinics:** 32.0%

- **Top Facility Owners:**  
  - **Ministry of Health:** 4,138 facilities  
  - **Private Enterprise (Institution):** 1,252 facilities  

- **24-Hour Access:**  
  - **72.66%** of facilities classified as *Hospitals* operate 24 hours.  
  - **Nairobi** has the highest number of facilities **not** open 24/7 (720 facilities).

- **Leadership of Facilities:**  
  Most common job titles of facility staff in charge:  
  - **Clinical Officer:** 2,414 facilities  
  - **Nurse:** 1,675 facilities  

---

### 🌍 County-Level Analysis (Compared to 2019 Population)

By merging facility data with Kenya’s 2019 census, the analysis highlights major disparities in access and capacity.

#### **Access to Facilities (Density)**

- **Most Underserved Counties:**  
  - Mandera: **8.8 facilities per 100k people**  
  - Bungoma: **9.9 facilities per 100k people**

- **Best-Served Counties:**  
  - Nyeri: **55.6 facilities per 100k people**  
  - Kirinyaga: **36.0 facilities per 100k people**

#### **Patient Capacity (Beds + Cots)**

- **Lowest Patient Capacity Counties:**  
  - Mandera: **0.51 beds per 1,000 people**  
  - Kwale: **0.53**  
  - Wajir: **0.68**

- **Highest Capacity Counties:**  
  Nyeri, Lamu, Nairobi, Kisumu, and Bomet have the **highest beds per 1,000 people**.

- **Provincial/Regional Capacity:**  
  - **Rift Valley:** Highest total beds + cots (**15,677**)  
  - **North Eastern:** Lowest (**1,842**)

---

### 🩺 Key Service Gaps

Significant gaps exist across essential healthcare services among the 9,551 operational facilities:

- **Family Planning (FP):** Only **43.7%** of facilities offer FP services.
- **In-Patient Department (IPD):** Only **40.4%** offer in-patient care.
- **Home-Based Care (HBC):** Only **28.8%** have HBC services.
- **Anti-Retroviral Therapy (ART):** Only **9.6%** provide ART treatment.

#### **ART Clinic Density**
Counties with the highest number of ART clinics per 100,000 people:  
- Homa Bay
- Laikipia  
- Migori
- Kisumu
- Siaya
---

## 🧠 Key Takeaways

- **Primary care dominates Kenya’s healthcare landscape**, with Dispensaries and Medical Clinics accounting for over 77% of all operational facilities.  
- **Wide regional disparities exist in facility access**, with Mandera and Bungoma significantly underserved compared to counties like Nyeri and Kirinyaga.  
- **Patient capacity is critically low in several counties**, with Mandera, Kwale, and Wajir all having fewer than 0.7 beds per 1,000 people.  
- **Essential services are unevenly distributed**, as less than half of facilities offer FP, IPD, or HBC services, and only 9.6% provide ART.  
- **The Ministry of Health is the dominant provider**, owning more than one-third of all operational facilities nationwide.  
- **Urban counties like Nairobi show gaps in accessibility**, with many facilities not offering 24-hour services despite high facility numbers.


---

## 📌 How to Use This Project

1. Download or clone the repository.  
2. Open the notebook (`.ipynb`) in Jupyter, VS Code, or Google Colab.  
3. Run the cells to:
   - Load and clean the datasets  
   - Generate analytic tables  
   - Produce visualisations  
4. Extend the analysis to include:
   - GIS maps  
   - Service availability data  
   - County-level dashboards  

---

## ✔️ Requirements

Python 3.8+  

Libraries:
- pandas  
- numpy  
- matplotlib  
- seaborn  
- plotly (optional)  
- geopandas (optional for maps)

---

## 🏁 Future Improvements

- Add geospatial mapping for facility distribution  
- Merge service availability datasets (maternity, labs, beds, equipment)  
- Build a Streamlit or Power BI dashboard  
- Include staff and bed capacity performance metrics  

---

## 📬 Contact

If you use this analysis for policy, planning, or research, attribution is appreciated.


