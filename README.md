# Los Angeles Crime Data Analysis  
## Trends, Patterns, and Insights

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Dataset Overview](#dataset-overview)
3. [Data Cleaning & Preparation](#data-cleaning--preparation)
4. [Data Modeling](#data-modeling)
5. [Key Insights & Conclusions](#key-insights--conclusions)
6. [Future Work](#Future-Work)

---
## Project Overview

For this project, I chose to analyze crime data from the City of Los Angeles in order to identify meaningful trends, patterns, and actionable insights.

As we all know, crime and public safety are constantly discussed topics in the United States, particularly regarding gun violence and urban safety. Rather than relying on headlines, this project uses real data to better understand:

As we all know, crime in the United States is constantly in the headlines, especially when it comes to gun violence and public safety.
So I thought it would be interesting to explore this topic through real data and see what is actually happening on the ground.

My goal was to better understand where and when crime happens, who is affected by it, and how data can support smarter decision-making — both in terms of public policy and resource allocation.
Ultimately, if we think like a mayor or a police chief, resources are limited — patrol units, personnel, and budgets are not infinite. It’s not possible to invest equally across all areas.

That’s why it’s crucial to identify where problems are more severe, who is most affected, and where efforts should be concentrated — in order to act in a way that is efficient, targeted, and data-driven.
If we think like a mayor or police chief, resources are limited — patrol units, personnel, and budgets are not infinite. It is not possible to invest equally across all areas.

Therefore, identifying where problems are more severe and where targeted intervention is needed becomes essential for efficient, data-driven policy and resource allocation.

## Project Objectives

1. What are the main crime hotspots in Los Angeles, and which areas require more targeted resource allocation?

2. How did crime patterns change over the years, and how did COVID-19 affect crime levels and types?

3. What are the spatial and temporal patterns of crime — which days, hours, and premise types experience higher crime levels?

4. Who are the main victim groups from a demographic perspective, and how can that help design smarter prevention and intervention programs?
   
---

## Dataset Overview
The dataset is based on official crime reports from the Los Angeles Police Department, published through the city’s open data portal.

It includes over one million crime incidents reported between 2020 and 2025. Each row represents a single crime event and contains 28 variables.

The dataset is based on official crime reports from the Los Angeles Police Department (LAPD), published through the city’s Open Data Portal.

It includes over one million reported crime incidents between 2020 and 2025, with each row representing a unique crime event and containing 28 different variables.

- **Source:** LAPD (via Data.gov)  
- **Time Coverage:** 2020–2025  
- **Original Records:** 1,004,991  
- **Variables:** 28  
- **Unit of Analysis:** One reported crime incident per row  

Dataset link:  
🔗 https://catalog.data.gov/dataset/crime-data-from-2020-to-present

For accurate year-over-year comparison, the final analytical period was restricted to **2020–2023**, resulting in approximately **877,000 high-quality records**.

## Data Dictionary 
- `DR_NO ` – Unique crime report ID
- `Date Rptd` – Date the crime was reported to the police
- `DATE OCC` – Date the crime actually occurred
- `TIME OCC` – Time the crime occurred (24-hour format)
- `AREA` – LAPD geographic area code
- `AREA NAME` – LAPD area name
- `Rpt Dist No` – Reporting district number within LAPD area
- `LOCATION` – Street address (approximate, often block-level)
- `Cross Street` – Nearest cross street (if available)
- `LAT` – Latitude of the crime location
- `LON` – Longitude of the crime location
- `Part 1-2` – Crime severity classification:  
    -	1 = Serious (violent / major property)  
    -	2 = Less serious
- `Crm Cd` – Primary crime code
- `Crm Cd Desc` –  Text description of the primary crime
- `Crm Cd 1` – Same as primary crime code (often duplicated)
- `Crm Cd 2` – Secondary crime code (if multiple crimes occurred)
- `Crm Cd 3` – Third crime code (if applicable)
- `Crm Cd 4` – Fourth crime code (if applicable)
- `Vict Age` – Victim’s age
- `Vict Sex` – Victim’s sex
- `Vict Descent` – Victim’s descent code
- `Premis Cd` – Code for the type of location (e.g., street, apartment)
- `Premis Desc` – Description of the premises
- `Mocodes` – Modus Operandi codes – how the crime was committed (comma-separated)
- `Weapon Used Cd` – Code for weapon used (if any)
- `Weapon Desc` – Description of the weapon
- `Status` – Short status code (e.g., IC, AA)
- `Status Desc` – Full description of case status (e.g., Investigation Continued, Adult Arrest)

## Data Cleaning & Preparation
### 1️⃣ Standardization & Validation
- Converted column names to **snake_case** format to ensure consistent, analysis-friendly column names.
- Verified uniqueness of `dr_no`.
- Confirmed absence of duplicate records.
- Renamed ambiguous fields (`part_1-2` → `crime_severity`).
- Removed these 2 columns: `mocodes`, `cross_street` because they were not relavant to my analysis and also they had a large amount of missing values 
--- 
### 2️⃣ Data Completeness
To ensure fair annual comparisons:
- **2025 was excluded** (only 97 records → incomplete year)
- **2024 was excluded** due to a sharp and sustained decline in later months, likely reflecting incomplete or delayed reporting rather than a real crime drop

Final analytical period : **2020–2023 (~877K records)**

--- 
### 3️⃣ Missing Values & Outliers

**Geographic Data**
- ~0.22% missing coordinates.
- Records retained.
- Added `has_coordinates` flag for map filtering.

**Case Status**
- 8 records with unknown status removed (<0.001%).

**Weapon Data**
- Missing values assigned placeholder code (500).
- Labeled as “UNKNOWN WEAPON / OTHER WEAPON.”

**Victim Age**
- Ages ≤ 0 or > 100 treated as invalid and set to missing.

---
### 4️⃣ Feature Reduction & Grouping
The original dataset was highly granular. While detailed information can be useful, excessive fragmentation introduces statistical noise, reduces interpretability, and makes it difficult to identify meaningful macro-level trends.

Since the objective of this project was to analyze structural crime patterns rather than extremely specific reporting distinctions, dimensionality reduction was necessary.

For example, the dataset contained 140 separate crime descriptions, including minor variations such as “theft of a bicycle under $50” versus “theft of a bicycle over $50.”
Although technically different, this level of detail does not meaningfully contribute to trend analysis and instead obscures broader behavioral patterns.

To improve analytical clarity and robustness, I implemented the following transformations:

- Grouped 140 crime descriptions into 13 high-level analytical categories, including:
Fraud, Theft, Robbery, Burglary, Assault & Battery, Aggravated Assault, Domestic Violence, Sex Crimes, Kidnapping, Human Trafficking, Vandalism, Public Order & Legal, and Criminal Homicide.

- Consolidated 306 premise types into 14 broader environmental categories, such as:
Residential, Commercial, Transportation, Education, Healthcare, Government, Industrial, and others.
This allows clearer spatial pattern detection across environment types.

- Grouped 80 weapon types into 9 standardized categories:
Firearm, Knife/Cutting, Blunt Object, Physical Force, Threat/Intimidation, Chemical/Fire, Vehicle, Animal, and Unknown/Other.
This reduces categorical sparsity and improves interpretability in violence analysis.

- Standardized victim sex to consistent categories (M, F)
Invalid or inconsistent entries were treated as missing to ensure downstream analytical reliability.

- Clustered victim descent into five broader demographic groups:
Hispanic, White, Black, Asian, and Other.

These transformations significantly reduced dimensional complexity while preserving analytical meaning, allowing clearer trend detection, improved dashboard readability, and more actionable insights.

---
### 5️⃣ Feature Engineering (Power Query)

To enrich analytical flexibility and enable deeper insights, additional derived features were created:

- Time of Day Classification
(Morning, Afternoon, Evening, Night)

- Hour of Occurrence

- Victim Age Groups
(0–18, 19–30, 31–50, 51–65, 65+)

- Report Lag = date_occ - date_rptd 
(Days between crime occurrence and police report)

- Population Data Integration
Added population per demographic group to calculate per-100K victimization rates, enabling normalized comparative analysis rather than raw counts.
Source: https://en.wikipedia.org/wiki/Los_Angeles

---
## Data Modeling

To enable scalable analysis and efficient reporting, I designed the dataset using a star schema architecture.

The model consists of:

- A centralized FactCrime table containing all measurable crime events

- Multiple connected dimension tables

- One-to-many relationships between each dimension and the fact table

- A dedicated Calendar dimension to support advanced time intelligence analysis
<img width="600" height="500" alt="image" src="https://github.com/user-attachments/assets/0759f093-bf2a-406e-a88a-75c8eb597eef" />


## Power BI Dashboard 
- Developed interactive dashboards with cross-filtering and drill-through analysis.

- to built a custom area-level map visualization i used the LAPD Devisons 

Data source:
LAPD Divisions – City of Los Angeles GeoHub
https://geohub.lacity.org/datasets/lapd-divisions/explore

Click to watch the interactive dashboard demo [- 
https://youtu.be/6ruybptJ5Jc](https://www.youtube.com/watch?v=ePPF7NDxydA) 

<img width="2344" height="1302" alt="Screenshot 2026-02-18 113617" src="https://github.com/user-attachments/assets/634b3dab-89fb-4bd7-a232-0147fad30f9e" />

<img width="2330" height="1313" alt="Screenshot 2026-02-18 113921" src="https://github.com/user-attachments/assets/daaf864c-7a33-4700-aabc-c61b5a435e89" />

<img width="2337" height="1318" alt="Screenshot 2026-02-18 113948" src="https://github.com/user-attachments/assets/932f1632-1fd5-4854-a06f-4aac934e0604" />


## Key Insights & Conclusions

✔ Crime dropped sharply during the first lockdown (March 2020). After restrictions were lifted, crime rebounded and stabilized at higher levels.

Suggests temporary mobility suppression rather than structural crime elimination.


✔ Central reports the highest number of crimes, Followed by 77th Street.

Residential areas show the highest volume, althoug that can be missleading because when we look at Industrial areas thry show the highest severity proportion.


Crime peaks during weekends, especially Fridays.

Firearm-related crimes occur mainly between 10 PM – 5 AM.


Fraud increased dramatically post-COVID.

COVID-19 did not create fraud — it accelerated it due to:

Economic distress

Government relief fund opportunities

Rapid transition to digital activity

Fraud shows an extremely high unsolved rate (~96%).

Domestic Violence

74% of victims are women.

Most affected age group: 31–50.

Peaks in evening hours.

Seasonal increase in July–August.

Possible explanations:

Increased time at home

Financial stress

Higher temperatures (correlated with aggression)

Per 100K Crime Rates (Example: 77th Street)

Black residents: ~6,361 per 100,000

Hispanic residents: ~992 per 100,000

(Important: Demographic data represents victims only, not offenders.)
---

## Future Work
Finally, I considered several future research directions that can be intersering :

1. examining the relationship between crime and socioeconomic factors such as unemployment rate, average income, and education level ect.
   This would help understand not just where crime happens — but why.

2. analyzing external factors such as weather, holidays, protests, special events, which may influence crime patterns.

3. expanding the dataset to include years 2010–2019 to identify long-term trends and compare different economic or social periods. (https://data.lacity.org/Public-Safety/Crime-Data-from-2010-to-2019/63jg-8b9z/about_data) 

4. And if we can clearly map the past, the next logical question is: can we also map the future?
This can be done using time series forecasting models such as SARIMA or Prophet, which identify trends and seasonality to estimate future crime levels by type and area.
This represents a proactive approach — moving from reacting to crime after it happens, to anticipating and preventing it before it occurs.
