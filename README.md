# econ5200-lab02-deflation

# Index Integrity — Deflation, Substitution Bias & Goodhart

## Objective

This project audits the integrity of a real-dollar deflation pipeline and the behavioral metrics built on top of it, quantifying two distinct sources of systematic error — index mislabeling and upper-level substitution bias — while documenting a live instance of Goodhart's Law in an engagement metric.

## Methodology

- Conducted an end-to-end audit of an existing deflation pipeline, tracing intermediate outputs against known base-year benchmarks to identify discrepancies
- Diagnosed and resolved four distinct bugs, including a base-year mislabeling defect in which series expressed in 1982-84 dollars were incorrectly reported as 2020 dollars
- Re-validated corrected outputs against independent price index series to confirm base-year alignment
- Computed annualized growth rates for CPI-U and C-CPI-U over the sample period to isolate upper-level substitution bias
- Decomposed the CPI-U/C-CPI-U growth-rate differential (in percentage points per year) from the raw index-point gap, and articulated why the two quantities are not interchangeable (index-point gaps compound and are level- and horizon-dependent, whereas the annualized pp differential is a normalized rate)
- Analyzed the joint distribution of DAU/MAU and time-per-session over time to detect a sign change in their correlation, consistent with metric gaming following the metric's adoption as a target (Goodhart's Law)
- Refactored the corrected deflation logic into `deflation_utils.py`, exposing a tested `deflate_series()` function with unit tests covering base-year conversion, edge cases, and regression against the original bug set
- Built an interactive index-integrity monitor to visualize deflated series, flag base-year mismatches, and surface the CPI-U/C-CPI-U divergence over time

## Key Findings

- The deflation pipeline contained four defects that, left uncorrected, would have systematically misstated real-dollar values; the most significant produced 1982-84-dollar output mislabeled as 2020 dollars, materially distorting any real-terms comparison
- Upper-level substitution bias was measurable and persistent: CPI-U grew at **2.61%/year** versus C-CPI-U at **2.35%/year**, a differential of **.27 pp/year**. This annualized rate differs from the raw index-point gap of **2.7** points/year, because the pp/year figure is a normalized, compounding-adjusted rate while the point gap reflects the two indices' absolute levels and is not directly comparable across time horizons
- A correlation flip between DAU/MAU and time-per-session — from **+0.93** to **-0.96** — was identified as a signature of Goodhart's Law, indicating that optimization pressure on the DAU/MAU target altered the underlying user behavior it was meant to proxy for
- Delivered a tested, reusable `deflate_series()` utility and an interactive monitor, providing ongoing safeguards against recurrence of the identified index-integrity failures
