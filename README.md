# migraine-tools-performance

**Repository author:** Ghaith Al‑Gburi

**Status:** draft / in-development — power-estimation tools and example outputs

---

## Quick view
This repository contains an R Markdown analysis that estimates statistical power for several migraine-related outcomes using a bootstrap + Wilcoxon approach. The primary analysis notebook is `estimate_power.Rmd`; a rendered HTML report with the results (if present) will be in the `Power results/` directory (for example `Power results/estimate_power.html`).

---

## Purpose
The goal of this repository is to provide a self-contained, reproducible workflow to:

- estimate statistical power for a set of migraine outcomes under an imposed shift between two groups using a bootstrap-based Wilcoxon test; and
- provide a small, easy-to-run analysis pipeline that can be re-run on either the supplied example input (`R_input.csv`) or on full study data (`data.csv`).

This repository is intended as a lightweight toolkit for planning and verifying power for future migraine studies.

> **Data privacy:** this repository should **not** include identifiable participant data. The file `R_input.csv` should contain only the minimal, anonymised fields necessary for power estimation. The full study data (if added) should be placed in `data.csv` and handled according to institutional privacy rules.

---

## Files in this repo
- `estimate_power.Rmd` — R Markdown notebook that runs the bootstrap power estimation (this is the script you pasted).
- `Power results/` — suggested folder for rendered outputs (HTML, CSV summaries).
- `data collection tool/` — (planned) directory where survey / data collection artifacts will be stored.
- `R_input.csv` — **required** minimal input for the power script (one-row-per-participant, only the columns used by `estimate_power.Rmd`).
- `data.csv` — (optional) the full study dataset (kept separate from the minimal `R_input.csv`).
- `README.md` — this file.

---

## Required R version & packages
- R >= 4.0 (recommended)

The `estimate_power.Rmd` uses only base R plus commonly installed packages for reading and rendering. Recommended packages:

```r
install.packages(c("rmarkdown", "knitr", "readr"))
```

(If you prefer `read.csv()` from base R, no extra packages are strictly required to run the core bootstrap routine.)

---

## Input file format (`R_input.csv`)
The power script expects a CSV with the following columns (exact names used in `estimate_power.Rmd`):

```
gh, hc, pf, rf_p, rf_e, sf, bp, ef, ew, m4, msq, ichd3b, migraine_status
```

Notes and expectations:
- Outcome columns (`gh`, `hc`, `pf`, `rf_p`, `rf_e`, `sf`, `bp`, `ef`, `ew`) should be numeric and contain the measured outcome values.
- Grouping columns (`m4`, `msq`, `ichd3b`) are expected to contain two levels labelled (for example) `Negative` and `Positive`. The code will warn if both levels are not present.
- `migraine_status` is treated specially: rows with `migraine_status == "Negative"` are dropped before comparisons so the script compares the remaining groups.

If you make `R_input.csv` by subsetting your full dataset, ensure column names match exactly (case-sensitive).

---

## What `estimate_power.Rmd` does (short)
- Clears the environment and reads `R_input.csv`.
- Defines a set of outcomes and grouping variables.
- For each outcome × grouping variable pair the script:
  - extracts two groups (enforcing an order where possible),
  - resamples (with replacement) `Nsim` times two samples of the observed sizes, adding an imposed `shift` value to the second group,
  - performs a Wilcoxon rank-sum test on each resample, and
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

Suggested to save results after running the notebook:

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

## Adding your data
You mentioned you will add a `data collection tools/` directory and upload two CSVs: `R_input.csv` (minimal input for power estimation) and `data.csv` (full study data). Place the minimal file at the repository root or update the `read.csv()` path inside the Rmd accordingly.

---

## License
This repository is released under the **GNU Affero General Public License v3.0**.

---

## Contact
**Author:** Ghaith Al‑Gburi — ghaith.ali.khaleel@gmail.com

---

*If you want, I can also prepare a short `git` snippet to add & commit this README, or a small `Makefile` to standardize rendering and output saving.*

