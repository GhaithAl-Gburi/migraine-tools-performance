# The relation between migraine assessment tools performance and quality of life among undergraduate college students in Iraq

**Repository authors:** Ghaith Al‑Gburi, Mustafa Al-Gburi

**Status:** completed

---

## Quick view
Click to view the full results of the power estimation (rendered HTML via githack):  
[Open power estimation results — rendered HTML (githack)](https://raw.githack.com/GhaithAl-Gburi/migraine-tools-performance/main/estimate_power.html)

---

## Purpose
This repository contains the R Markdown notebook and supporting output files that provides a self-contained, reproducible workflow to estimate statistical power for a set of migraine outcomes under an imposed shift between two groups using a bootstrap-based Wilcoxon test. The shift value is kept at 5, corresponding to the minimal clinically important difference reported for the SF-36 quality of life domains. 

> **Data privacy:** this repository does **not** include participant‑level identifiable data.

---

## Files in this repo
- `estimate_power.Rmd` — R Markdown notebook that runs the bootstrap power estimation.
- `estimate_power.html` — a rendered analysis summary documenting the results of power estimation.
- `data collection tool/` — a directory containing the research survey and supporting documents.
- `R_input.csv` — the input file required for power estimation. (To be added following publication)
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

## Input file format (`R_input.csv`)
The power script expects a CSV with the following columns (exact names used in `estimate_power.Rmd`):

```
gh, hc, pf, rf_p, rf_e, sf, bp, ef, ew, m4, msq, ichd3b, migraine_status
```

Notes and expectations:
- Outcome columns (`gh`, `hc`, `pf`, `rf_p`, `rf_e`, `sf`, `bp`, `ef`, `ew`) should be numeric and contain the measured SF-36 domains values.
- Grouping columns (`m4`, `msq`, `ichd3b`) are expected to contain two levels labelled `Negative` and `Positive`. The code will warn if both levels are not present.
- `migraine_status` is treated specially: rows with `migraine_status == "Negative"` are dropped before comparisons so the script compares the remaining groups.

---

## What the script does (high-level)
- Clears the environment and reads `R_input.csv`.
- Defines a set of outcomes and grouping variables.
- For each outcome × grouping variable pair the script:
  - extracts two groups (enforcing an order where possible),
  - resamples (with replacement) `Nsim` times two samples of the observed sizes, adding an imposed `shift` value to the second group,
  - performs a Wilcoxon rank-sum test on each resample
  - computes the empirical power as the fraction of bootstrap replicates with p-value < `alpha`.
- Returns a table with estimated power and an approximate 95% CI for each outcome–assessment pair.

Default parameters (modifiable in the Rmd):
- `Nsim <- 5000`
- `alpha <- 0.05`
- `shift <- 5`
- `set.seed(123)`

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

If you prefer, extract the R code and run it as a script:

```r
knitr::purl("estimate_power.Rmd", output = "estimate_power.R")
source("estimate_power.R")
```

**Adjusting parameters**
Open `estimate_power.Rmd` and change `Nsim`, `alpha`, `shift`, or the `outcomes` / `group_vars` vectors before rendering.

---

## Output
The script prints a `results` data frame containing, for each outcome × assessment pair:
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
- Validate `R_input.csv` column names and levels before running; small typos in column names will cause errors.

---

## License
This repository is released under the **GNU Affero General Public License v3.0**.

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

