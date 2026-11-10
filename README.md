
# Swiss Road Accident Severity Analysis
**Data Analytics Project | ZHAW**

**Research Question:**  
Which factors are associated with road accident severity in Switzerland, and what role does the urban-rural classification play?

**Hypotheses:**
- H1: Accidents in rural areas are more likely to be severe than accidents in urban areas.
- H2: Accidents at night are more likely to be severe than accidents during the day.

---

# 1. Data

## 1.1 Data Sources
- ASTRA: Swiss road accident data (2021–2025)
- BFS: Municipality urban-rural classification

## 1.2 Data Collection
- Swiss road accident is retrieved thru the STAC-API from data.geo.admin 
- BFS: Municipality data is revtrieved from the csv from bfs.admin.ch

## 1.3 Data Cleaning
- Identified missing values, datatypes and duplicates
- Preperation for municipality "GDE-Nummer" for the integration
- Renamed Columns for prequet and final data set

## 1.4 Data Integration
- The data from BFS Municipality was merged (left join) with the traffic accident data on the gde number
- 89'544 accidents could be paired with the correct municipality 351 (0,39) accidents could not be assigned
- Added catagorical variable: area_type
- Added two binäry variables: severe and night

## 1.5 Feature Engineering
- severe: 0=leichte Verletzung, 1=schwere/tödliche Verletzung 
- night: 0=Tag, 1=Nacht(22-06 Uhr)
- area_type: Urban, Intermediate, Rural 

## 1.6 Data Storage
- The parquet data set features the data without any missing values.
- The entire accident data set was stored in the sql-database.

## 1.7 Data Quality
351 Values could not be assigned with a BFS-Municipality. Data loss of 0.39% 

---

# 2. Exploratory Data Analysis (EDA)

## 2.1 Dataset Overview
<!-- Descriptive statistics and distributions -->

## 2.2 Accident Severity
<!-- Distribution of severity levels -->

## 2.3 Urban vs. Rural
<!-- Severity differences across municipality types -->

## 2.4 Day vs. Night
<!-- Severity differences by time of day -->

## 2.5 Additional Factors
<!-- Accident types, road types and temporal patterns -->

## 2.6 Statistical Tests
<!-- Chi-square tests and significance -->

---

# 3. Modeling

## 3.1 Model Selection
<!-- Why logistic regression? -->

## 3.2 Model Preparation
<!-- Target variable, predictors and train/test split -->

## 3.3 Model Training
<!-- Logistic regression implementation -->

## 3.4 Model Evaluation
<!-- Confusion matrix, precision, recall, ROC-AUC -->

## 3.5 Model Interpretation
<!-- Odds ratios and important predictors -->

---

# 4. Conclusions

## 4.1 Key Findings
<!-- Most important results -->

## 4.2 Hypothesis Evaluation
<!-- H1 and H2: supported or not supported? -->

## 4.3 Limitations
<!-- Data quality, assumptions, methodological limitations -->

## 4.4 Final Conclusion
<!-- Answer to the research question -->

---

## AI Usage Documentation
<!-- How was AI used during the project? -->
