# Data Collection & Preprocessing

This document describes the data sources, acquisition methodology, and cleaning procedures used to construct the Pennsylvania county-level opioid panel dataset.

---

## Table of Contents

1. [Overview](#overview)
2. [Data Sources](#data-sources)
3. [Collection Methodology](#collection-methodology)
4. [Cleaning & Processing — By Source](#cleaning--processing--by-source)
5. [Final Integration](#final-integration)
6. [Notes on Missing Data](#notes-on-missing-data)

---

## Overview

The dataset integrates twelve thematic data sources spanning federal health agencies, economic bureaus, state law enforcement systems, and public health monitoring programs. Together these sources provide county-year coverage of opioid mortality outcomes, socioeconomic conditions, drug supply indicators, healthcare infrastructure, and criminal justice activity across Pennsylvania.

Three acquisition methods were used depending on source availability:

- **Direct download** — standardized CSV/Excel files retrieved from agency portals
- **Web scraping** — programmatic extraction from dashboards lacking bulk-download options
- **Fixed-width / text-file parsing** — custom extraction from structured but non-tabular file formats

---

## Data Sources

| Domain | Dataset | Agency | Format | Access Method |
|---|---|---|---|---|
| Mortality | Multiple Cause of Death (MCOD) | CDC | Fixed-width / CSV | Direct download |
| Mortality (provisional) | Vital Statistics Rapid Release (VSRR) | CDC | CSV | Direct download |
| Economic | County GDP | Bureau of Economic Analysis (BEA) | Excel (wide) | Direct download |
| Economic | Personal Income (PI) | BEA | Excel (wide) | Direct download |
| Demographics | Population & Age Structure | NIH SEER Program | Fixed-width text | Custom parsing |
| Labor Market | Unemployment & Labor Force | Bureau of Labor Statistics (BLS) | CSV (annual) | Direct download |
| Prescription Supply | PDMP Dispensation Records | OpenData PA | CSV | Direct download |
| Prescription Supply | ARCOS Transaction Database | DEA – ARCOS Portal | CSV | Direct download |
| Illicit Drug Supply | Drug Seizure Records | OpenData PA | CSV | Direct download |
| Non-Fatal Overdoses | Overdose Information Network (ODIN) | OpenData PA | CSV | Direct download |
| Healthcare Access | Area Health Resources File (AHRF) | HRSA | Fixed-width text (annual) | Custom parsing |
| Medicare Utilization | Geographic Variation Public Use File | CMS | CSV / Excel | Direct download |
| Crime & Public Safety | UCR Crime Dashboard | Pennsylvania State Police | Dashboard (no bulk download) | Web scraping |

---

## Collection Methodology

### Direct Downloads

The following datasets were retrieved directly from their respective agency portals as CSV or Excel files and required minimal restructuring to enter the processing pipeline:

- County GDP and Personal Income (BEA)
- Unemployment and labor force statistics (BLS, one file per year)
- PDMP dispensation records (OpenData PA)
- Opioid seizure and arrest data (OpenData PA)
- ODIN overdose incidents (OpenData PA)
- Medicare Geographic Variation files (CMS)
- ARCOS transaction data (DEA)

These sources were already organized into usable tabular structures, enabling straightforward integration once column names and county identifiers were harmonized.

### Web Scraping — UCR Crime Data

The Pennsylvania State Police UCR Crime Dashboard does not offer a bulk download. Annual county-level statistics were therefore extracted programmatically: each county-year combination was queried individually via the dashboard's API endpoints, results were stored as JSON files, and the full collection was subsequently converted into a structured tabular dataset. County names were then mapped to FIPS codes using a standard crosswalk.

### Fixed-Width / Text-File Parsing — SEER & AHRF

Population demographic data from the NIH SEER Program and healthcare access data from the HRSA Area Health Resources File were provided as large fixed-width text files with values encoded at specific column positions. Custom extraction procedures were written for each source using the schema documentation provided by the agencies. Fields were parsed programmatically, mapped to their variable labels, and converted to tabular format before entering the cleaning pipeline.

---

## Cleaning & Processing — By Source

### Population Demographics (NIH SEER)

- Extracted fields from fixed-width text files using column-position schemas
- Mapped raw field codes to descriptive variable labels
- Aggregated fine-grained demographic subcategories into broader analytical groups (age bands, race/ethnicity, sex)
- Saved structured output in tabular format for merging

### Opioid Mortality (CDC MCOD + VSRR)

- Loaded raw CSVs from both CDC WONDER (MCOD) and VSRR provisional death files
- Replaced `"Suppressed"` string entries with `NaN` (small-cell suppression for counties with fewer than ~10 deaths)
- Renamed variables and standardized structure to include `CountyFIPS`, `Year`, and total opioid deaths
- Appended the two sources into a single mortality panel, with VSRR records used to extend coverage into more recent years where MCOD files lag
- **Suppressed counts were not imputed** — they remain missing to avoid introducing bias in rural counties

### Economic Data — GDP & Personal Income (BEA)

- Downloaded as wide-format files with years as columns
- Reshaped into long (three-column) format: `CountyFIPS`, `Year`, value
- Standardized variable names across years for consistency

### Labor Market (BLS)

- Annual files were downloaded separately for each year
- Combined into a multi-year dataset
- Extracted two key indicators: total labor force count and annual unemployment rate
- Applied consistent naming conventions across years

### Healthcare Access (HRSA AHRF)

- Provided as annual fixed-width text files with accompanying variable dictionaries
- Custom extraction mappings were developed for each year to account for schema changes across editions
- Variables merged across all years, reshaped into a unified panel
- Missing provider counts preserved (not interpolated)

### Medicare Utilization (CMS)

- Two separate file series: 2007–2013 and 2014–2023
- Used accompanying CMS documentation to identify Pennsylvania-relevant county-level variables
- Extracted and harmonized variable names across both series
- Combined into a single continuous panel after confirming consistency of definitions across the two periods

### Crime Data (Pennsylvania UCR)

- Scraped county-year records from the State Police dashboard (see Collection Methodology above)
- JSON records converted to tabular structure
- County names mapped to FIPS codes via standard crosswalk
- Column names standardized to match the overall schema

### Opioid Seizures & Arrests (OpenData PA)

- Downloaded directly as CSV files
- Aggregated to county-year level: incident counts, drug quantities (kg), and arrest counts were summed
- County-year combinations with no recorded activity were filled with zeros (absence of a record was treated as no activity, not missing data)
- Annual per-incident averages computed for quantity-per-seizure variables
- Variable names standardized

### PDMP Dispensation Records (OpenData PA)

- Downloaded as CSV files with one record per dispensation event
- Pivoted to county-year aggregates for total dispensations and total prescriptions
- **Note:** Buprenorphine is excluded from PDMP records in Pennsylvania, as it is regulated separately under DATA 2000 waivers. This means treatment-related opioid dispensing is not captured in these counts.

### ARCOS Transaction Data (DEA)

- Filtered to retain only retail dispensing transactions (transaction code `"S"` — sales to dispensing firms such as pharmacies and hospitals), excluding wholesale distributor-to-distributor transfers
- Aggregated by county, year, and drug type to produce annual supply volumes (weight in kg and quantity)
- Total opioid weight and quantity computed as sums across all drug categories
- All variables renamed for consistency

---

## Final Integration

After cleaning each source independently, all datasets were merged on `CountyFIPS` and `Year` to construct the final county-level analytical panel. The merge was performed sequentially, with the mortality panel serving as the base.

### Post-Merge Transformations

**Per-population normalization.** Count variables (providers, hospital beds, crime incidents, dispensations, etc.) were divided by county population and multiplied by 10,000 to produce per-10,000-population rates. This allows meaningful cross-county comparisons given Pennsylvania's wide variation in county size.

**Volatility measures.** Year-over-year percent changes were computed for key indicators including mortality, unemployment, GDP, total crime, heroin and fentanyl incident counts, dispensations, and prescriptions. These capture dynamic trends alongside static levels and are denoted with a `_vol` suffix.

**GDP per capita.** Derived as county GDP divided by total population.

---

## Notes on Missing Data

Missing values in the final dataset arise from several sources:

- **CDC suppression:** Counties with fewer than ~10 opioid deaths in a given year have counts suppressed by CDC to protect privacy. These appear as `NaN` in the `Deaths` column and are flagged by the `Flag` variable.
- **PDMP exclusions:** Buprenorphine is excluded from Pennsylvania PDMP reporting. Dispensation counts may understate total opioid activity in high-treatment counties.
- **UCR non-reporting:** Not all local law enforcement agencies submit data to the UCR program every year. Gaps in crime variables reflect reporting absence, not necessarily an absence of crime.
- **AHRF gaps:** Some healthcare capacity variables have gaps in earlier years where provider counts were not collected or reported for small counties.

In all cases, missing values are preserved as `NaN` in the final dataset. **No imputation was performed** to avoid artificially smoothing gaps in historical reporting.
