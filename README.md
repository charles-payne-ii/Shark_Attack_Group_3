# Shark Cage Experience: Oh My!

**A shark-incident data cleaning and exploratory analysis project**  
Ironhack Data Analytics Mini Project | Group 3  
**Team:** Marinela, Cem, and Charles

## Project overview

This project uses historical shark incident records to practise cleaning a real-world dataset with Python and pandas, then to identify patterns by **country, state/region, activity, attack type, and recorded fatal outcome**.

Our proposed business case is a **shark-cage diving experience**. We use the incident data to identify coastal regions that may warrant further research into potential locations for the experience. These findings are exploratory: incident counts alone do **not** establish that a destination is safe, suitable for cage diving, or commercially viable.

### Business question

> Where are shark incidents most frequently recorded, what activities are most commonly associated with them, and how do unprovoked incidents and fatal outcomes vary across locations?

## Dataset

- **Source:** [Global Shark Attack File (GSAF) incident log](https://www.sharkattackfile.net/incidentlog.htm)
- **Input file used in the notebook:** `GSAF5.xls`
- **Original size:** 7,125 records and 23 columns
- **Final analytical dataset:** 7,107 records and five columns
- **Unit of analysis:** One recorded shark incident per row, subject to the quality of the original source

The final notebook creates a pandas DataFrame named `shark_data_final` with these columns:

| Field | Description |
| --- | --- |
| `country` | Country where the incident was recorded, formatted as lowercase text with underscores |
| `state` | State or region where the incident was recorded, similarly formatted |
| `activity` | Broad activity category derived from the original free-text description |
| `type` | Cleaned incident classification: `unprovoked`, `provoked`, or `unknown_reason` |
| `fatal` | Cleaned reported outcome: `yes`, `no`, or `unknown` |

The original source includes additional information such as dates, species, injury descriptions and source references. These were examined during scoping but are not part of the final five-column analysis.

## Research questions and hypotheses

| Question / hypothesis | How we examined it | Outcome |
| --- | --- | --- |
| Some locations account for more recorded shark incidents than others. | Counts and rankings by country and by country–state combination | Supported as a **descriptive concentration** in the available records |
| Swimming, surfing, fishing and other water activities appear frequently in incident reports. | Standardized activity labels and frequency counts | Supported as a **frequency pattern**; not a measure of per-participant risk |
| Most recorded incidents are not fatal. | Counts of `yes`, `no`, and `unknown` in the fatality column | Supported among records with known outcomes |
| Shark attacks vary seasonally. | Considered the Date column and date/Regex cleaning | **Not tested** in the final analysis because date quality and project scope prevented a reliable comparison |
| Species is associated with attacks or fatalities. | Reviewed the species descriptions | **Not tested**; the field was excluded from the final dataset |

## Cleaning and transformation workflow

The analysis is documented in `Group_3_Final.ipynb`. Its main steps are:

1. **Inspect the raw data.** Use `.head()`, `.shape`, `.info()`, `.nunique()`, and `.unique()` to understand the file and identify inconsistent values.
2. **Select relevant fields.** Remove fields not required for the chosen geographic, activity, incident-type and fatality questions.
3. **Standardize column names.** Convert names to lowercase and replace spaces with underscores.
4. **Clean attack types.** Use `.str.lower()`, `.str.strip()`, a mapping dictionary and `.map()` to combine inconsistent descriptions into broader classes. Rows with missing `type` values are excluded.
5. **Clean locations.** Standardize country and state names through whitespace removal, lowercasing and underscore replacement.
6. **Clean fatality labels.** Map reported `Y` / `N` values to `yes` / `no`; classify several nonstandard or missing values as `unknown`.
7. **Group activities.** Fill missing activity descriptions with `Other`, then apply `clean_activity()` to look for fragments such as `swim`, `snork`, `fish`, `surf`, `div`, `wad`, `stand`, and `bath` and assign broader categories.
8. **Build the final dataset.** Retain the five analytical fields and use filtering, `groupby()`, `.size()`, `value_counts()`, sorting, mapping and merging to compare locations and outcomes.

**Note on categorization:** Activity grouping is intentionally rule-based and approximate. Some multi-activity descriptions, ambiguous terms or unmatched branches can be assigned to a broad category or remain missing. The counts reflect those cleaning rules and should not be interpreted as perfectly classified activities.

**Note on attack types:** The notebook groups source records labeled `Sea Disaster` into `unprovoked`. This was a project cleaning decision, not an independently verified reclassification; it affects the reported unprovoked total.

### Date-cleaning obstacle

The original Date column has **6,159 distinct values**, with inconsistent formats and incomplete or uncertain dates. We researched basic Regex and Python date parsing to extract date components but did not complete a reliable seasonal analysis within the project timeframe. Rather than present an unsupported seasonal finding, we excluded Date from the final analysis and recorded this limitation.

## Exploratory data analysis and findings

The analysis primarily involves **categorical data**, so frequency tables, grouping and comparisons are more appropriate than correlation coefficients between numeric measurements.

### Countries with the most recorded incidents

| Country | Recorded incidents |
| --- | ---: |
| United States | 2,589 |
| Australia | 1,536 |
| South Africa | 598 |

Together, these three countries account for **4,723 of 7,107 records (approximately 66.5%)** in the final dataset.

### Most frequent cleaned activity categories

| Activity | Recorded incidents |
| --- | ---: |
| Surfing | 1,419 |
| Fishing | 1,349 |
| Swimming | 1,292 |

These are **counts in incident records**, not the probability of an attack while participating in an activity. The classification is based on simplified text-matching rules, and some entries do not receive one of the named activity categories.

### Attack classification and recorded outcomes

| Attack type | Records |
| --- | ---: |
| Unprovoked (as classified by the notebook) | 5,513 |
| Provoked | 649 |
| Unknown reason | 945 |

| Reported fatal outcome | Records |
| --- | ---: |
| No | 4,977 |
| Yes | 1,492 |
| Unknown | 638 |

Approximately **77.6%** of the final records are classified as unprovoked under the project's rules. Fatal outcomes are recorded as `yes` for **about 21.0%** of all 7,107 records, while **about 9.0%** have an unknown outcome. These figures are descriptive and are affected by the cleaning decisions above.

### Geographic examples for further business investigation

The presentation highlights the state/region with the highest recorded incident count in each of the three leading countries:

| Country | State or region | Total recorded incidents | Recorded fatal incidents | Fatal incidents / total |
| --- | --- | ---: | ---: | ---: |
| United States | Florida | 1,197 | 48 | ~4.0% |
| Australia | New South Wales | 523 | 104 | ~19.9% |
| South Africa | KwaZulu-Natal | 219 | 49 | ~22.4% |

These figures are **not measures of destination safety or cage-diving risk**. The fatality percentages use all recorded incidents in each region as the denominator and do not adjust for unknown outcomes or exposure.

## Business interpretation

The historical records suggest that Florida, New South Wales and KwaZulu-Natal are possible **starting points for further feasibility research**, not recommended or validated operating sites. Before evaluating a shark-cage operation, we would need data on:

- Reliable shark sightings and species in specific coastal areas
- Permits, local rules and environmental protections
- Existing tourism operators, accessibility and customer demand
- Equipment, operating conditions, professional safety requirements and costs
- Exposure and reporting differences that make incident counts difficult to compare

Our findings help prioritize questions for future investigation; they do not establish demand, predict future shark encounters or prove that one area is safer than another.

## How to run the notebook

### Requirements

- Python 3 and Jupyter Notebook or JupyterLab
- `pandas`, `numpy` and `xlrd` (the Excel reader needed for `.xls` files)

Install the packages in your Python environment:

```bash
python -m pip install pandas numpy xlrd notebook
```

### Run

1. Download or clone the repository.
2. Place **`GSAF5.xls` in the same folder** as `Group_3_Final.ipynb`. If the dataset is not distributed with the repository, obtain it from the GSAF source and save it under this filename.
3. Start Jupyter Notebook:

   ```bash
   jupyter notebook
   ```

4. Open `Group_3_Final.ipynb`.
5. Choose **Run All Cells**, from top to bottom. The notebook reads `GSAF5.xls` using `pd.read_excel(..., engine="xlrd")` and creates `shark_data_final` during the cleaning workflow.
6. Review the output tables for country rankings, activity frequencies, attack types, fatality counts and country–state breakdowns.

**Troubleshooting:** A `FileNotFoundError` usually means `GSAF5.xls` is not in the notebook's working directory. A `NameError` for `shark_data_final` usually means an earlier notebook cell has not run successfully in the current kernel.

## Project files and presentation

| File | Purpose |
| --- | --- |
| `Group_3_Final.ipynb` | Main source of code, cleaning logic and EDA outputs |
| `GSAF5.xls` | Original input dataset |
| `README.md` | Project overview, methodology, reproduction steps, findings and limitations |
| `Shark Cage Experience.pdf` | Nine-slide project presentation (when included in the repository) |

The Ironhack submission instructions also require reusable functions in a `.py` file, an **online presentation link**, and a personal GitHub repository named **`data-cleaning-pandas`**. The attached notebook currently defines `clean_activity()` inside the notebook; the corresponding `.py` module and a shareable online slide URL should be added to the submission if not already included. No online slide URL has been supplied here, so one is not invented in this README.

## Limitations and lessons learned

- **Historical records are not exposure-adjusted.** Countries with more incidents may have more water users, more coastline, different reporting practices or longer records.
- **Descriptive association is not causation or prediction.** Counts cannot establish which activity or destination has the greatest risk to an individual.
- **Dates and species were excluded.** The seasonal and species-related hypotheses remain untested.
- **Reporting and missing data matter.** Some outcomes are unknown; missing attack types were removed; state labels and activity descriptions are not fully standardized.
- **Simplified categories entail trade-offs.** Substring matching supports a short project but can misclassify complex descriptions and leave some activity values uncategorized.
- **Regional comparison is preliminary.** State-level totals do not identify individual beaches or prove the feasibility or safety of a shark-cage experience.

A central lesson from this project was to define questions that the available data can actually answer, keep cleaning decisions traceable, and state clearly where the evidence stops.

## Contributors and acknowledgements

**Group 3:** Marinela, Cem, and Charles  
**Course:** Ironhack Data Analytics  
**Data:** Global Shark Attack File (GSAF)

This project was completed as an educational data-wrangling and exploratory analysis exercise.
