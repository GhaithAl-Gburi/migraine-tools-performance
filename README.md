# README —  Migraine Screening Tools Performance Empirical Power Estimation

**Repository authors:** Ghaith Al‑Gburi, Mustafa Al-Gburi

**Study DOI / citation:** Unpublished

---

## Quick view
Click to view the full analysis results:
[Open the full analysis report (PDF)](https://raw.githack.com/GhaithAl-Gburi/migraine-tools-performance/main/analysis_report.pdf)

---

## Purpose
This repository contains `estimate_power.R` — an R script used to perform empirical statistical power estimation for a set of migraine outcomes under an imposed shift between two groups using a bootstrap-based Wilcoxon test. The shift value is kept at 5, corresponding to the minimal clinically important difference reported for the SF-36 quality of life domains. 

> **Data privacy:** this repository does **not** include participant‑level identifiable data.

---

## Files in this repo
- `estimate_power.R` — R script.
- `estimate_power.Rmd` — R Markdown file (script + narrative + results).
- `analysis_report.pdf` — Rendered PDF report for the complete analysis workflow.
- `data collection tool/` — Directory containing the research survey and supporting documents.
- `migraine_screening_dataset.csv` — the input file required for power estimation. (To be added following publication)
- `data.csv` — the full study dataset. (To be added following publication)
- `LICENSE` — AGPL-3.0 license.
- `README.md` — this file.

---

## Required R version & packages
- R >= 4.0 (recommended)

The `estimate_power.Rmd` uses only base R plus commonly installed packages for reading and rendering. Recommended packages:

```r
install.packages(c("rmarkdown", "knitr", "readr"))
```

---

## Expected data format
The power analysis script expects a CSV file with the following columns:

| Category                          | Variable names                                                                   |
|-----------------------------------|----------------------------------------------------------------------------------|
| SF-36 outcome variables           | `gh`, `hc`, `pf`, `rf_p`, `rf_e`, `sf`, `bp`, `ef`, `ew`                       |
| Binary grouping variables         | `m4`, `msq`, `ichd3b` (`Negative`, `Positive`)                                 |
| Migraine status grouping variable | `migraine_status`                                                               |

---

## What the script does (high-level)
1. **Reads** the dataset.  
2. **Defines analysis variables and parameters:**  
   - Specifies SF-36 outcome variables (`gh`, `hc`, `pf`, `rf_p`, `rf_e`, `sf`, `bp`, `ef`, `ew`).  
   - Specifies grouping variables (`m4`, `msq`, `ichd3b`, `migraine_status`).  
   - Sets simulation parameters (`Nsim`, `alpha`, `shift`) and random seed.  
3. **Empirical power estimation:**  
   - For each outcome × assessment method pair, extracts two comparison groups.  
   - Generates a null distribution of Wilcoxon rank-sum statistic using bootstrap resampling.  
   - Generates an alternative distribution by imposing a location shift on the second group.  
   - Performs a Wilcoxon rank-sum test on each simulated sample.  
   - Estimates empirical statistical power as the proportion of simulated tests with p-value < `alpha`.  
4. **Power uncertainty estimation:**  
   - Computes an approximate 95% Monte Carlo confidence interval for the estimated power.  
5. **Outputs:**  
   - A table containing estimated power and confidence intervals for each outcome × assessment method pair.

---

## How to run
From the repository root you can render the notebook to HTML (recommended) or run the R code directly.

**Render the R Markdown to HTML**

```r
# requires rmarkdown
rmarkdown::render("estimate_power.Rmd")
```

This will produce an HTML file (for example `estimate_power.html`) which contains the printed `results` table and any other outputs in the document.

**Run the core code as plain R**

If you prefer, run the code as a script:

```r
source("estimate_power.R")
```

**Adjusting parameters**
Open `estimate_power.Rmd` and change `Nsim`, `alpha`, `shift`, or the `outcomes` / `group_vars` vectors before rendering.

---

## Output
The script prints a `results` data frame containing, for each outcome × assessment method pair:
- `Outcome`, `Assessment`, `Level1`, `Level2`, `Power`, `CI_low`, `CI_high`.

To save the result as a CSV file run the following code:

```r
dir.create("Power results", showWarnings = FALSE)
write.csv(results, "Power results/power_estimates.csv", row.names = FALSE)
```

---

## Reproducibility & tips
- Keep a fixed seed when producing power estimates (the Rmd uses `set.seed(123)` by default).
- If you plan to run very large `Nsim` values on a server, consider saving intermediate results and/or running the bootstrap in parallel (the current script runs sequentially).
- Validate `migraine_screening_dataset.csv` column names and levels before running; small typos in column names will cause errors.

---

## License & citation

**License:** This repository is released under the **AGPL-3.0 License**.

**How to cite this code:**  

```

```

---

## Contact
- **Author:** Ghaith Al‑Gburi
- **Email:** ghaith.ali.khaleel@gmail.com
- **GitHub:** `https://github.com/GhaithAl-Gburi` 
- **ORCID:** `0000-0001-7427-8310` 

- **Co-author:** Mustafa Al‑Gburi
- **Email:** mustafa.ali.gburi@gmail.com
- **GitHub:** `https://github.com/mustafagburi` 
- **ORCID:** `0009-0009-2699-8351` 
---

