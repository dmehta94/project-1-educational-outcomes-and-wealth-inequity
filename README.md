# Education Outcomes and Wealth Inequality: A Global EDA

**Author:** Deval Mehta &nbsp;|&nbsp; **Context:** General Assembly Data Science Bootcamp — Project 1 &nbsp;|&nbsp; **Stack:** Python, pandas, Matplotlib, Seaborn, NumPy

---

## What It Does

This project explores whether educational attainment and wealth inequality move together across a 14-country sample over the 20-year span from 1998 to 2017, using data from [Gapminder](https://www.gapminder.org/about/) and the [World Inequality Database](https://wid.world/). I clean and align six datasets — compulsory education duration, Bachelor's degree attainment by sex, Gini index, and top-10% pre-tax income share — then visualize country-level trends to assess whether a covariance signal is strong enough to justify further modeling work. The short answer is no, not with this data — but the analysis surfaces exactly why, and what would be needed to get there.

---

## Why I Built This

**Context:** This was Project 1 of the General Assembly Data Science Bootcamp, the first substantial analytical project I completed in the program. At this point in the curriculum I had covered Python fundamentals, pandas, and basic data visualization — but not yet statistical modeling. The project brief asked us to pick datasets from Gapminder and pose a question. I came in with a specific one: given the amount the US spends per student relative to its educational outcomes, is wealth inequality part of the story?

**Problem:** The United States [spends more per student than nearly any country in the world](https://nces.ed.gov/blogs/nces/post/education-at-a-glance-2023-putting-u-s-data-in-a-global-context), yet upper secondary completion sits around 87% — comparable to Lithuania, which spends roughly 3/8 as much per student. This gap suggests that spending alone doesn't determine outcomes. Wealth inequality is one plausible explanatory factor: in theory, a more concentrated wealth distribution limits economic mobility, which limits educational access, which feeds back into further concentration. I wanted to see whether the data supported investigating that feedback loop.

**Solution approach:** I sourced six datasets from Gapminder and the World Inequality Database, aligned them to a common 14-country sample for 1998–2017 (the constraint was Gini index data availability), and produced time-series visualizations of each variable. The goal wasn't a model — it was a data quality and signal check: is there enough of a trend in the inequality variables to make a regression meaningful?

**Results:** Education attainment rose consistently across the sample. Wealth inequality — measured by both the Gini index and top-10% income share — showed no consistent directional trend. Some countries stagnated, some oscillated, a few rose. Without a coherent inequality trend to match against the education trend, the covariance question can't be answered with this data. I documented what data and methods would be needed to revisit the question meaningfully.

---

## What I Learned

**Technical skills**
- Writing manual implementations of statistical functions (`mean`, `stdev`) from first principles — the kind of exercise that builds genuine intuition for what libraries are actually doing
- Multi-dataset alignment: using one dataset (Gini index) as the anchor for country selection across all others, rather than trying to merge on a sparse union
- Transposing DataFrames before passing to `sns.lineplot()` to plot country-level time series — a non-obvious pandas pattern that I still reach for in EDA
- Building reusable cleaning functions (`trim_to_countries_of_interest`) instead of repeating operations manually

**Data science insights**
- Data availability is a research design constraint, not just a cleaning nuisance. The Gini index limited the sample to 14 countries — some high-income, some middle-income, none low-income. That selection bias means the findings (or lack thereof) don't generalize beyond this specific slice of the income distribution
- Inconclusive results are legitimate findings. The absence of a clear inequality trend is informative: it tells you that this question needs either a longer time horizon, a different inequality measure, or a different analytical frame (e.g., cross-sectional rather than longitudinal)
- Visualizing both the raw time series and the aggregate (mean over time) together tells a richer story than either alone

**Software engineering practices**
- This was my first project using pandas in earnest. Post-bootcamp standardization surfaced a number of `inplace=True` calls and missing `random_state` equivalents — patterns I've since corrected in subsequent projects
- I defined the year range as a list in one cell and reconstructed it by hand in another. Defining shared constants once (as `YEAR_COLUMNS`) and referencing them everywhere is a habit I established in later projects

**Unexpected learnings**
- The standard deviation dictionary comprehension had a subtle bug: `trimmed_gini_index.columns[1:]` skipped `1998` and included the `time_averaged_value` column I had added earlier, meaning `stdev()` ran on the wrong set of columns. Catching this during standardization reinforced how much a module-level constant (`YEAR_COLUMNS`) would have prevented the issue entirely
- Gender doesn't appear to drive variation in Bachelor's attainment rates within this sample — the male and female trends track closely. That's its own finding, though it may reflect the narrow country sample more than a global pattern

**Design decisions**
- I chose to limit the sample to countries with *complete* Gini index data rather than imputing. With only 14 fully-observed countries available, imputing the Gini for additional countries would have introduced more noise than signal
- I excluded Peru despite it meeting the Gini completeness criterion because the pre-tax income share dataset had no data for it — another reminder that cross-dataset completeness is the binding constraint
- I used the 1998–2017 window rather than maximizing the historical range, because older legislation (repealed or superseded) would have complicated the interpretation of any inequality–education relationship

---

## Quick Start

```bash
# Clone the repository
git clone https://github.com/dmehta94/project-1-educational-outcomes-and-wealth-inequity.git
cd project-1-educational-outcomes-and-wealth-inequity

# Install dependencies
pip install numpy pandas matplotlib seaborn jupyter

# Launch the notebook
jupyter notebook code/project1-Deval.ipynb
```

**Data:** All datasets are included in the `data/` directory. Raw source files (from Gapminder and WID) and cleaned, trimmed versions are both present. The notebook reads from the raw files and writes the trimmed versions — running it end-to-end will overwrite the existing trimmed CSVs, which is fine.

**Directory structure:**
```
project-1-educational-outcomes-and-wealth-inequity/
├── code/
│   └── project1-Deval.ipynb    # Main analysis notebook
├── data/
│   ├── duration_compulsary_education.csv
│   ├── educ_attainment_ba_female.csv
│   ├── educ_attainment_ba_male.csv
│   ├── educ_attainment_ba_total.csv
│   ├── inequality_index_gini.csv
│   ├── wid_pretax_income_share_top10.csv
│   ├── trimmed_*.csv            # Cleaned versions written by the notebook
│   └── population.csv           # Provided but not used in final analysis
└── README.md
```

---

## Sample Output

The notebook produces six time-series line plots — one per dataset — showing country-level trends from 1998 to 2017. The education attainment plots show consistent upward trends across almost all 14 countries. The Gini and top-10% income share plots are more varied: the US and Russia show rising inequality across the period, while several European countries are relatively stable. No country shows a clearly falling inequality trend, which is the key null finding.

---

## Technical Details

**Stack:** Python 3, pandas, NumPy, Matplotlib, Seaborn

**Datasets and sources:**

| Dataset | Source | Description |
|---|---|---|
| `duration_compulsary_education.csv` | Gapminder | Years of compulsory education by country and year |
| `educ_attainment_ba_total.csv` | Gapminder | % of population 25+ with at least a Bachelor's degree |
| `educ_attainment_ba_female.csv` | Gapminder | Same, female population only |
| `educ_attainment_ba_male.csv` | Gapminder | Same, male population only |
| `inequality_index_gini.csv` | Gapminder | Gini coefficient by country and year |
| `wid_pretax_income_share_top10.csv` | World Inequality Database | Pre-tax income share held by top 10% |

**14-country sample:** Belarus, Canada, Costa Rica, El Salvador, France, Georgia, Germany, Indonesia, Luxembourg, Moldova, Panama, Russia, UK, USA — selected because these are the only countries with complete Gini index data for all 20 years in the analysis window.

**Key functions:**

- `mean(number_list)` — manual mean implementation (bootcamp exercise; demonstrates understanding of the underlying computation)
- `stdev(number_list)` — manual population standard deviation, building on `mean()`
- `abbrev_num_to_float(abbrev_number)` — converts abbreviated numeric strings (e.g., `'5M'`, `'45K'`) to floats; used for cleaning the population dataset
- `trim_to_countries_of_interest(df)` — filters a DataFrame to the 14-country sample and sets country as the index

---

## Limitations

- **The sample is too small and too selected for strong conclusions.** 14 countries with complete Gini data is a sparse and non-random sample — it over-represents middle- and high-income countries and under-represents the parts of the world where both education access and wealth inequality show the most variation
- **No formal correlation or regression analysis.** The project was scoped to EDA visualization only. A Pearson or Spearman correlation between the time-averaged Gini and time-averaged Bachelor's attainment per country would have been a natural next step
- **The education attainment data is sparse.** Only Canada and the USA have complete 20-year coverage in the Bachelor's attainment datasets — most other countries have gaps, which limits what can be inferred from the line plots
- **Compulsory education duration changed little.** Duration of compulsory education barely varied across countries or over time in this sample, making it uninformative as a predictor. A more useful measure might have been secondary school enrollment or completion rates
- **No log transform on population data.** The `abbrev_num_to_float()` function was written for cleaning the population dataset, but population was ultimately not used in the analysis. Including population as a weighting factor (e.g., population-weighted inequality measures) would have been an improvement

---

## Credits

This project was completed independently as part of the General Assembly Data Science Bootcamp (Project 1). Data sourced from [Gapminder](https://www.gapminder.org/) and the [World Inequality Database](https://wid.world/). Post-bootcamp documentation, code standardization, and this README were produced in collaboration with Claude AI (Anthropic, 2025).

---

## License

MIT License.

**Contact:** [GitHub @dmehta94](https://github.com/dmehta94) | [LinkedIn](https://www.linkedin.com/in/devalmehta94)