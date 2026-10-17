# Reading Anomaly Rates After Reform: Kenyan Public Procurement

Code for my working paper on how Kenya's 2024 procurement reforms changed the share of flagged contracts.

**Author:** Donie Paul Golanda
**Data (Zenodo):** https://doi.org/10.5281/zenodo.21191468
**Paper:** not yet posted. The link will be added here when it is.

## What this project does

I take contract records from Kenya's Public Procurement Regulatory Authority (PPRA) and flag contracts that look irregular. I then track the monthly share of flagged contracts around two 2024 reforms:

1. An upgraded Public Procurement Information Portal (August 2024).
2. A Public Procurement Capacity Building Levy of 0.03% (from 1 September 2024).

There is no untreated comparison group, so I use interrupted time-series regression on 18 monthly points (January 2024 to June 2025).

The main finding is that the flagged share falls from about 46% to 9%. Most of the fall comes from one flag, missing supplier. The other flags rise after the portal upgrade. I read the drop as a gain in record quality. I do not read it as proof that irregular contracting declined.

## How to run it

1. Open `Paper1_Anomaly_Detection_Approach_for_a_Resource_Limited_System.ipynb` in Google Colab (File, then Upload notebook).
2. Choose Runtime, then Run all. You can also run it one cell at a time.

The notebook loads the six data files straight from Zenodo. It needs no local files and no login. It runs from top to bottom in order.

Every step has a plain-language comment at the top that says what it does and why.

## The data

Source: Open Contracting Partnership Data Registry.
Original publisher: Kenya Public Procurement Regulatory Authority (PPRA).
Format: OCDS (Open Contracting Data Standard).

| File | What it holds |
|---|---|
| `awards_2024.csv`, `awards_2025.csv` | Contract award records |
| `awards_suppliers_2024.csv`, `awards_suppliers_2025.csv` | The supplier linked to each award |
| `main_2024.csv`, `main_2025.csv` | Buyer and tender (procurement method) records |

The files are on Zenodo, not in this repository.

Things to know about the data:

- The pull has 40,681 awards. The study window (January 2024 to June 2025) holds 39,349 of them (96.7%).
- July to December 2025 is incomplete (294 contracts in six months), and 633 records are stamped 2026. I leave these out of the window and label them in the notebook.
- Before January 2024 the registry has only 405 contracts, and only 14 in 2023. The pre-reform period cannot be extended.
- The awards file has no status field, so I cannot tell whether a missing supplier means no supplier was awarded or only that none was recorded.

## What the notebook does

| Steps | What happens |
|---|---|
| 1–7 | Load the six files, stack them, merge buyer and supplier details, and turn dates into real dates |
| 8–11 | Build the flags: missing supplier, zero value, direct procurement, Z-score above 3, and Isolation Forest. Combine them into one "is flagged" column |
| 12–14 | Monthly flag rate, the late-2025 check, and the final study window |
| 15–18 | The two regression models (single reform, and two reforms separated) with plots |
| 19–21 | What a missing supplier means, and an Isolation Forest refit without the supplier column |
| 22–23 | Robustness: other contamination rates, July 2024 dropped, and each flag on its own |
| 24–27 | Data checks (late rows, missing values, the July 2024 spike) and Figure 1 |
| 28–29 | Did the contracts or the buyers change after the reforms? |
| 30–31 | Is there usable data before 2024? Placebo test on fake reform dates, and the July check on all outcomes |
| 32 | A rounding check on one p value |

## Settings I chose

- Isolation Forest contamination: 5%, with `random_state=42`. I also test 1% and 10%.
- Z-score flag: more than 3 standard deviations.
- Reform dates: August 2024 (portal) and September 2024 (levy).
- Standard errors: ordinary least squares, with no autocorrelation correction. Durbin–Watson statistics are between 1.9 and 2.5.

## Files the notebook saves

The notebook writes a few small CSV files as it runs: `monthly_flag_rate_2024_2025.csv`, `monthly_flag_rate_final.csv` and `flag_rates_by_type.csv`.

## Tools

Python in Google Colab, with these package versions: pandas 2.2.3, scikit-learn 1.6.1 and statsmodels 0.15.0. The notebook also uses NumPy, SciPy and matplotlib.

## How to cite

Golanda, D. P. (2026). *Kenya PPRA public procurement data, 2024–2025* [Data set]. Zenodo. https://doi.org/10.5281/zenodo.21191468
