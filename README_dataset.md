# Dataset Reference — Data Dictionary

This document describes the final merged county-level panel dataset. It covers structure, coverage, variable definitions, units, and any variable-specific caveats.

---

## Table of Contents

1. [Dataset Overview](#dataset-overview)
2. [Structure & Identifiers](#structure--identifiers)
3. [Variable Groups](#variable-groups)
   - [Identifiers & Geography](#identifiers--geography)
   - [Demographics](#demographics)
   - [Economic Indicators](#economic-indicators)
   - [Labor Market](#labor-market)
   - [Healthcare Access (AHRF)](#healthcare-access-ahrf)
   - [Medicare Utilization (CMS)](#medicare-utilization-cms)
   - [Crime & Public Safety (UCR)](#crime--public-safety-ucr)
   - [Prescription Drug Supply (PDMP)](#prescription-drug-supply-pdmp)
   - [Opioid Supply Transactions (ARCOS)](#opioid-supply-transactions-arcos)
   - [Illicit Drug Supply & Seizures](#illicit-drug-supply--seizures)
   - [Non-Fatal Overdoses (ODIN)](#non-fatal-overdoses-odin)
   - [Opioid Mortality](#opioid-mortality)
   - [Derived & Volatility Variables](#derived--volatility-variables)
4. [Missing Data](#missing-data)
5. [Notes on Units & Normalization](#notes-on-units--normalization)

---

## Dataset Overview

| Attribute | Value |
|---|---|
| **File** | `data/final/county_panel.csv` |
| **Unit of observation** | County-year |
| **Geography** | Pennsylvania counties (67) |
| **Panel identifiers** | `CountyFIPS`, `Year` |
| **Primary outcome** | `Deaths` — opioid mortality rate per 10,000 population |
| **Total variables** | 150+ |

---

## Structure & Identifiers

All variables are at the **county-year** level. The dataset is in long format — one row per county per year.

Most count variables are expressed **per 10,000 population** to enable cross-county comparisons. See [Notes on Units & Normalization](#notes-on-units--normalization) for details.

---

## Variable Groups

---

### Identifiers & Geography

| Variable ID | Variable Name | Type | Description |
|---|---|---|---|
| `Year` | Year | Number | Year of the observation |
| `CountyFIPS` | County FIPS | Character | 5-digit county FIPS code (state + county). Unique county identifier. |
| `CZ` | Commuting Zone | Character | Commuting Zone code. Groups economically integrated counties for regional analysis. |
| `Population` | Population | Number | Total county population (raw count, not per-10,000) |

---

### Demographics

Source: **NIH SEER Program**. All variables are counts per 10,000 population.

**Age Structure**

| Variable ID | Description |
|---|---|
| `Age_0_4_Y` | Population aged 0–4 |
| `Age_5_9_Y` | Population aged 5–9 |
| `Age_10_14_Y` | Population aged 10–14 |
| `Age_15_19_Y` | Population aged 15–19 |
| `Age_20_24_Y` | Population aged 20–24 |
| `Age_25_29_Y` | Population aged 25–29 |
| `Age_30_34_Y` | Population aged 30–34 |
| `Age_35_39_Y` | Population aged 35–39 |
| `Age_40_44_Y` | Population aged 40–44 |
| `Age_45_49_Y` | Population aged 45–49 |
| `Age_50_54_Y` | Population aged 50–54 |
| `Age_55_59_Y` | Population aged 55–59 |
| `Age_60_64_Y` | Population aged 60–64 |
| `Age_65_69_Y` | Population aged 65–69 |
| `Age_70_74_Y` | Population aged 70–74 |
| `Age_75_79_Y` | Population aged 75–79 |
| `Age_80_84_Y` | Population aged 80–84 |
| `Age_85_Plus_Y` | Population aged 85 and over |

**Race & Ethnicity**

| Variable ID | Description |
|---|---|
| `Origin_H` | Hispanic origin |
| `Origin_NH` | Non-Hispanic origin |
| `Race_AIAN` | American Indian or Alaska Native |
| `Race_AS` | Asian |
| `Race_B` | Black |
| `Race_W` | White |

**Sex**

| Variable ID | Description |
|---|---|
| `Sex_F` | Female |
| `Sex_M` | Male |

---

### Economic Indicators

Source: **Bureau of Economic Analysis (BEA)**

| Variable ID | Variable Name | Type | Description |
|---|---|---|---|
| `Personal Income` | Personal Income | Number | Total personal income in thousands of dollars (raw county total, not per-10,000) |
| `Per Capita PI` | Personal Income Per Capita | Number | Personal income divided by county population (dollars per person) |
| `GDP` | Gross Domestic Product | Number | County GDP in thousands of dollars (raw county total) |
| `GDP Per Capita` | GDP Per Capita | Number | County GDP divided by total population (dollars per person) |

> **Note:** GDP and Personal Income are raw county totals (not per-10,000 normalized) given their nature as stock measures of economic output and income.

---

### Labor Market

Source: **U.S. Bureau of Labor Statistics (BLS)**

| Variable ID | Variable Name | Type | Description |
|---|---|---|---|
| `Labor Force` | Labor Force | Number | Number of people in the labor force per 10,000 population |
| `UnemploymentRate` | Unemployment Rate | Number | Annual county unemployment rate (percentage) |

---

### Healthcare Access (AHRF)

Source: **HRSA Area Health Resources File**. All provider and facility counts are per 10,000 population.

**Physicians**

| Variable ID | Variable Name | Description |
|---|---|---|
| `Total_Active_MDs` | Tot Active M.D.s Non-Fed & Fed | All active Doctors of Medicine (federal and non-federal) |
| `Total_MDs` | Total M.D.'s, Tot Non-Fed & Fed | Total M.D.s including inactive |
| `Emergency_Patn_Care` | Emergency Med, Total Patn Care | Emergency medicine physicians |
| `Psychiatry_Tot` | Psychiatry, Total | Psychiatrists |
| `MD_Less_35` | Total M.D.'s, < 35 | M.D.s under 35 years old |
| `MD_35_44` | Total M.D.'s, 35–44 | M.D.s aged 35–44 |
| `MD_45_54` | Total M.D.'s, 45–54 | M.D.s aged 45–54 |
| `MD_55_64` | Total M.D.'s, 55–64 | M.D.s aged 55–64 |
| `MD_65_74` | Total M.D.'s, 65–74 | M.D.s aged 65–74 |
| `MD_75_Plus` | Total M.D.'s, 75+ | M.D.s aged 75 and over |

**Facilities**

| Variable ID | Variable Name | Description |
|---|---|---|
| `Total_Hospitals` | Total Number Hospitals | Hospitals |
| `Hospital_Beds` | Hospital Beds | Hospital beds |
| `Nursing_Facility_Beds` | Nursing Facilities Total Beds | Nursing facility beds |
| `Home_Health_Agencies` | # Home Health Agencies | Home health agencies |
| `Comm_Mental_Health_Ctrs` | # Community Mental Health Ctrs | Community mental health centers |

---

### Medicare Utilization (CMS)

Source: **CMS Geographic Variation Public Use File**. Covers Medicare Fee-for-Service (FFS) beneficiaries.

| Variable ID | Variable Name | Type | Description |
|---|---|---|---|
| `BENES_FFS_CNT` | FFS Beneficiaries | Number | Count of FFS Medicare beneficiaries per 10,000 population |
| `BENE_AVG_AGE` | Average Age | Number | Average age of FFS Medicare beneficiaries |
| `BENES_ER_VISITS_PCT` | % Beneficiaries with ED Visit | Number | % of FFS beneficiaries with at least one ED visit during the year |
| `BENE_DUAL_PCT` | Percent Eligible for Medicaid | Number | % of FFS beneficiaries also eligible for Medicaid for at least one month |
| `TOT_MDCR_STDZD_PYMT_AMT` | Total Standardized Medicare Payment | Number | Total FFS Medicare payment (geographically adjusted) per 10,000 population |
| `TOT_MDCR_STDZD_PYMT_PC` | Standardized Per Capita Medicare Payment | Number | Per capita FFS Medicare payment, geographically adjusted |
| `IP_MDCR_PYMT_PER_USER` | IP Per User Actual Medicare Payment | Number | Per-user FFS Medicare payment for hospital inpatient services |
| `BENES_OP_PCT` | % Beneficiaries Using OP | Number | % of FFS beneficiaries using hospital outpatient services |
| `ASC_MDCR_STDZD_PYMT_AMT` | ASC Standardized Medicare Payment | Number | FFS Medicare payment for Ambulatory Surgery Center services (geographically adjusted) per 10,000 |
| `IRF_MDCR_STDZD_PYMT_AMT` | IRF Standardized Medicare Payment | Number | FFS Medicare payment for Inpatient Rehabilitation Facility services (geographically adjusted) per 10,000 |
| `IP_MDCR_PYMT_AMT` | IP Actual Medicare Payment | Number | Actual FFS Medicare payment for hospital inpatient services per 10,000 |

---

### Crime & Public Safety (UCR)

Source: **Pennsylvania State Police Uniform Crime Reporting (UCR)**. All variables are counts per 10,000 population.

| Variable ID | Description |
|---|---|
| `Murder and Nonnegligent Manslaughter` | Murder and non-negligent manslaughter |
| `Manslaughter by Negligence` | Negligent manslaughter |
| `Rape` | Rape |
| `Robbery` | Robbery |
| `Aggravated Assault` | Aggravated assault |
| `Other Assaults - Simple` | Simple assault |
| `Burglary` | Burglary |
| `Larceny - Theft` | Larceny and theft |
| `Motor Vehicle Theft` | Motor vehicle theft |
| `Arson` | Arson |
| `Human Trafficking` | Human trafficking |

> **Coverage note:** UCR reporting is voluntary. Gaps in crime variables reflect non-reporting by local agencies in that year, not necessarily an absence of crime.

---

### Prescription Drug Supply (PDMP)

Source: **OpenData PA — Pennsylvania PDMP Dispensation Records**. Per 10,000 population.

| Variable ID | Variable Name | Type | Description |
|---|---|---|---|
| `opioid_dispensing_rate` | Opioid Dispensing Rate | Number | Opioid dispensing rate per 10,000 population |
| `Total_Dispensations` | Total Dispensations | Number | Total opioid dispensations |
| `Total_Prescriptions` | Total Prescriptions | Number | Total opioid prescriptions |

> **Important:** Pennsylvania PDMP data **exclude buprenorphine**, which is reported under a separate regulatory framework. These counts capture prescriptions and dispensations for controlled opioids excluding medication-assisted treatment (MAT) products.

---

### Opioid Supply Transactions (ARCOS)

Source: **DEA ARCOS Database** — restricted to transaction code `"S"` (sales to retail dispensing firms). Per 10,000 population unless otherwise noted.

**Weight of Opioids Sold to Dispensing Firms (kg)**

| Variable ID | Drug |
|---|---|
| `WT_BUPRENORPHINE` | Buprenorphine |
| `WT_CODEINE` | Codeine |
| `WT_DIHYDROCODEINE` | Dihydrocodeine |
| `WT_FENTANYL` | Fentanyl |
| `WT_HYDROCODONE` | Hydrocodone |
| `WT_HYDROMORPHONE` | Hydromorphone |
| `WT_LEVORPHANOL` | Levorphanol |
| `WT_MEPERIDINE` | Meperidine |
| `WT_METHADONE` | Methadone |
| `WT_MORPHINE` | Morphine |
| `WT_OPIUM, POWDERED` | Powdered Opium |
| `WT_OXYCODONE` | Oxycodone |
| `WT_OXYMORPHONE` | Oxymorphone |
| `WT_TAPENTADOL` | Tapentadol |

**Number of Sales of Opioids**

The `QS_*` variables parallel the `WT_*` variables above. They represent the **number of sales transactions** to opioid dispensing firms per 10,000 population for the same set of drugs:

`QS_BUPRENORPHINE`, `QS_CODEINE`, `QS_DIHYDROCODEINE`, `QS_FENTANYL`, `QS_HYDROCODONE`, `QS_HYDROMORPHONE`, `QS_LEVORPHANOL`, `QS_MEPERIDINE`, `QS_METHADONE`, `QS_MORPHINE`, `QS_OPIUM, POWDERED`, `QS_OXYCODONE`, `QS_OXYMORPHONE`, `QS_TAPENTADOL`

**Aggregate Supply Variables**

| Variable ID | Variable Name | Description |
|---|---|---|
| `Total_Opioids_Q_Weight` | Total Weight of Opioids | Total weight (kg) of all opioids sold to dispensing firms per 10,000 |
| `Total_Opioids_Q_Sales` | Total Number of Opioid Sales | Total number of sales transactions to dispensing firms per 10,000 |

---

### Illicit Drug Supply & Seizures

Source: **OpenData PA — Seizure & Arrest Records**. Per 10,000 population.

**Heroin**

| Variable ID | Variable Name | Description |
|---|---|---|
| `Heroin Incident Count` | Heroin Incident Count | Total heroin seizure incidents by law enforcement |
| `Average Heroin Quantity` | Average Heroin Quantity | Average quantity seized per incident (kg) |
| `Heroin Drug Quantity` | Heroin Drug Quantity | Total heroin seized (kg) |
| `Heroin Arrests` | Heroin Arrests | Total heroin arrests by law enforcement |

**Fentanyl**

| Variable ID | Variable Name | Description |
|---|---|---|
| `Fentanyl Incident Count` | Fentanyl Incident Count | Total fentanyl seizure incidents |
| `Average Fentanyl Quantity` | Average Fentanyl Quantity | Average quantity seized per incident (kg) |
| `Fentanyl Drug Quantity` | Fentanyl Drug Quantity | Total fentanyl seized (kg) |
| `Fentanyl Arrests` | Fentanyl Arrests | Total fentanyl arrests |

**Aggregate**

| Variable ID | Variable Name | Description |
|---|---|---|
| `Total_Incidents` | Total Incidents | Total seizure incidents (all drugs) per 10,000 |

> **Note:** Zero values in seizure variables indicate no recorded incidents in that county-year, not missing data. County-year combinations with no activity were explicitly set to zero during processing.

---

### Non-Fatal Overdoses (ODIN)

Source: **OpenData PA — Overdose Information Network (ODIN)**. Police-reported non-fatal overdoses. Per 10,000 population.

**By Drug Type**

| Variable ID | Description |
|---|---|
| `Drug_Pharma_Opioid` | Pharmaceutical opioid-related events |
| `Drug_Pharma_Other` | Other pharmaceutical drug-related events |
| `Drug_Pharma_Stimulant` | Pharmaceutical stimulant-related events |
| `Drug_Fentanyl` | Fentanyl-related events |
| `Drug_Heroin` | Heroin-related events |
| `Drug_Unknown` | Unknown drug-related events |
| `Drug_Pharma_Total` | Total pharmaceutical drug-related events |
| `Drug_Illicit_Total` | Total illicit drug-related events |

**Emergency Department Visits**

| Variable ID | Description |
|---|---|
| `Avg_ED_Visits_All` | Average ED visits for all causes |
| `Avg_ED_Visits_Heroin` | Average ED visits related to heroin |
| `Avg_ED_Visits_Stimulant` | Average ED visits related to stimulants |

**Survival Analysis Support**

| Variable ID | Description |
|---|---|
| `Survival_N` | Number of observations used in survival analysis |
| `n_quarters_observed` | Number of quarters with observed data in that county-year |

---

### Opioid Mortality

Source: **CDC MCOD + VSRR**

| Variable ID | Variable Name | Type | Description |
|---|---|---|---|
| `Deaths` | Opioid Mortality Rate | Number | Opioid-related deaths per 10,000 population |
| `Flag` | Missing Deaths Flag | Boolean | `True` if the county's death count was suppressed by CDC (small-cell suppression) and the value is missing |

> **Small-cell suppression:** CDC suppresses death counts for counties with fewer than approximately 10 deaths in a given year to protect individual privacy. Flagged observations have `Deaths = NaN`. These are most common in smaller rural counties and earlier years.

---

### Derived & Volatility Variables

The following variables are computed from the base variables and are included to support dynamic analysis. Volatility variables (`_vol` suffix) represent the **year-over-year percent change** in the corresponding base variable.

**Derived Economic Variables**

| Variable ID | Description |
|---|---|
| `GDP Per Capita` | County GDP / total population |

**Volatility Variables**

| Variable ID | Base Variable |
|---|---|
| `Population_vol` | Population |
| `GDP_vol` | GDP |
| `UnemploymentRate_vol` | Unemployment Rate |
| `Total Crime_vol` | Total crime count |
| `Deaths_vol` | Opioid mortality rate |
| `Heroin Incident Count_vol` | Heroin seizure incidents |
| `Average Heroin Quantity_vol` | Average heroin quantity per seizure |
| `Fentanyl Incident Count_vol` | Fentanyl seizure incidents |
| `Average Fentanyl Quantity_vol` | Average fentanyl quantity per seizure |
| `Total_Dispensations_vol` | Total opioid dispensations |
| `Total_Prescriptions_vol` | Total opioid prescriptions |
| `Total_Opioids_Quantity_vol` | Total opioid quantity (ARCOS) |

---

## Missing Data

| Source of Missingness | Affected Variables | Treatment |
|---|---|---|
| CDC small-cell suppression | `Deaths` | `NaN`; flagged by `Flag = True` |
| UCR non-reporting | All crime variables | `NaN` |
| AHRF historical gaps | Provider and facility counts (earlier years) | `NaN` |
| PDMP buprenorphine exclusion | `Total_Dispensations`, `Total_Prescriptions` | Structural undercount (not missing) |
| Seizure data — no activity | Heroin/Fentanyl variables | Explicit `0` (not missing) |

**No imputation was performed.** Missing values reflect genuine gaps in historical reporting.

---

## Notes on Units & Normalization

**Per-10,000-population normalization** is applied to count variables to allow fair comparisons across Pennsylvania's 67 counties, which vary from small rural counties (population in the thousands) to large urban ones (population over a million). The formula applied is:
```normalized_variable = (raw_count / Population) × 10,000```

Variables that are *not* normalized per 10,000 include:
- `Population` (absolute count)
- `Personal Income` and `GDP` (absolute dollar totals in thousands)
- `Per Capita PI`, `GDP Per Capita` (already per-person)
- `UnemploymentRate` (already a percentage)
- `BENE_AVG_AGE` (already a rate/average)
- Percentage variables from CMS (prefixed `BENE_*_PCT`)
- Volatility variables (percent changes, not counts)
