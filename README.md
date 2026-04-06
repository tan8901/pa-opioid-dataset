# Pennsylvania Opioid Crisis — County-Level Panel Dataset

> A data engineering project integrating multi-source administrative, public health, and economic data to support quantitative analysis of the opioid epidemic across Pennsylvania counties.

---

## Overview

The opioid crisis has been one of the most severe public health emergencies in modern U.S. history, with Pennsylvania consistently ranking among the most heavily affected states. Understanding the structural, economic, and supply-side drivers of opioid mortality requires granular, multi-dimensional data that crosses institutional boundaries — mortality records, prescription flows, illicit drug seizures, labor markets, healthcare access, and more.

This project constructs a **comprehensive county-level panel dataset** for Pennsylvania, integrating over a dozen federal and state administrative data sources into a single, analysis-ready file. The result is a longitudinal dataset spanning multiple years that can support econometric modeling, spatial analysis, machine learning, and epidemiological research on the opioid epidemic.

---

## Project Goals

- **Integrate** heterogeneous public health, economic, and law enforcement datasets into a unified analytical framework
- **Standardize** variables to per-10,000-population measures to account for county size differences
- **Preserve** data quality by retaining missing values rather than imputing, accurately reflecting gaps in historical reporting
- **Enable** rigorous quantitative analysis of opioid mortality determinants at the county level
- **Document** the full data pipeline transparently to support reproducibility and future extension

---

## Repository Structure

```
.
├── README.md                        ← You are here
├── data/
│   ├── raw/                         ← Original source files (unmodified)
│   ├── processed/                   ← Intermediate cleaned datasets by source
│   └── final/
│       └── county_panel.csv         ← Final merged analytical dataset
├── scripts/
│   ├── collection/                  ← Scraping and download scripts
│   ├── cleaning/                    ← Per-source cleaning scripts
│   └── merge.py                     ← Final integration pipeline
├── docs/
│   ├── README_data_collection.md    ← Data sources and processing methodology
│   └── README_dataset.md            ← Data dictionary for the final dataset
└── notebooks/                       ← Exploratory analysis (optional)
```

---

## Data at a Glance

| Dimension | Details |
|---|---|
| **Unit of observation** | County-year |
| **Geography** | Pennsylvania counties (67 counties) |
| **Identifier** | County FIPS code + Commuting Zone |
| **Key outcome** | Opioid mortality rate (per 10,000 population) |
| **Variable count** | 150+ variables across 12 thematic domains |

### Thematic Domains

| Domain | Source |
|---|---|
| Opioid Mortality | CDC MCOD / VSRR |
| Demographics & Population | NIH SEER Program |
| Economic Conditions | BEA (GDP, Personal Income) |
| Labor Market | U.S. Bureau of Labor Statistics |
| Prescription Drug Supply | DEA ARCOS, OpenData PA PDMP |
| Illicit Drug Supply & Seizures | OpenData PA |
| Non-Fatal Overdoses | OpenData PA ODIN |
| Healthcare Access | HRSA Area Health Resources File |
| Medicare Utilization | CMS Geographic Variation File |
| Crime & Public Safety | Pennsylvania UCR (State Police) |

---

## Quick Start

### Requirements

```bash
pip install pandas numpy requests beautifulsoup4
```

### Load the Final Dataset

```python
import pandas as pd

df = pd.read_csv("data/final/county_panel.csv")

# Key identifiers
print(df[["Year", "CountyFIPS", "CZ", "Population", "Deaths"]].head())
```

### Example: Opioid Mortality Over Time

```python
import matplotlib.pyplot as plt

state_avg = df.groupby("Year")["Deaths"].mean()
state_avg.plot(title="Average County Opioid Mortality Rate (per 10,000)", figsize=(10, 5))
plt.ylabel("Deaths per 10,000")
plt.tight_layout()
plt.show()
```

---

## Documentation

For full details on data sources, collection methodology, and processing decisions, see:

- **[`docs/README_data_collection.md`](docs/README_data_collection.md)** — Covers all data sources, acquisition methods, and cleaning procedures for each component dataset.
- **[`docs/README_dataset.md`](docs/README_dataset.md)** — Full data dictionary for the final merged panel, including variable definitions, units, and coverage notes.

---

## Key Design Decisions

**Per-population normalization.** Most count variables are expressed per 10,000 population to allow meaningful cross-county comparisons given Pennsylvania's wide variation in county size (from dense urban counties like Philadelphia to sparse rural ones).

**Missing values preserved.** Suppressed mortality counts (small-cell suppression in CDC data) and other gaps are retained as `NaN` rather than imputed. This is intentional — imputing suppressed values would introduce bias in sparse rural counties.

**Volatility measures included.** Year-over-year percent change variables are computed for key indicators (mortality, unemployment, crime, drug supply) to capture dynamic trends alongside levels.

**ARCOS filtered to retail transactions.** DEA ARCOS data are restricted to transaction code `"S"` (sales to dispensing firms) to focus on the retail opioid supply chain rather than wholesale transfers.

---

## Data Limitations & Caveats

- **Geographic scope:** This dataset covers Pennsylvania only. Findings may not generalize to other states with different healthcare systems, drug markets, or reporting infrastructures.
- **Suppressed mortality counts:** CDC suppresses death counts for small counties (typically fewer than 10 deaths). These appear as missing values and may disproportionately affect rural county coverage.
- **PDMP scope:** Pennsylvania PDMP data exclude buprenorphine dispensations, which are used for opioid use disorder treatment. This may understate total opioid dispensing in counties with high treatment penetration.
- **UCR reporting:** Crime data depend on voluntary submission by local agencies. Coverage may be incomplete for some county-years.
- **ARCOS aggregation:** ARCOS captures manufacturer-to-distributor and distributor-to-dispensing-firm flows, not individual prescriptions. It is a supply-side proxy, not a direct prescription measure.

---

## License

Please review the terms of use for each underlying data source before using this dataset for publication or redistribution. All source data are derived from U.S. federal and Pennsylvania state government repositories and are generally available for research use; consult individual agency data use agreements for specifics.

---

## Contact & Contributing

Contributions, corrections, and extensions are welcome. Please open an issue or submit a pull request. If you use this dataset in your research, a citation to this repository is appreciated.
