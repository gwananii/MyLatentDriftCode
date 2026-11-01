Latent Boundary Drift in Peaks-over-Threshold Risk Estimation under Stochastic Volatility — replication code
This repository contains the replication code for:
> Gwanani, I., L. Dhliwayo, S. Nadarajah, and S. Makurumidze. *Latent Boundary Drift in Peaks-over-Threshold Risk Estimation under Stochastic Volatility*. Manuscript submitted to the *Journal of Risk and Financial Management*.
The notebook `Latent Drift (1).ipynb` reproduces every table and figure in the paper and appendices in one run. It uses one data snapshot, one GARCH(1,1)-t estimator (Section 4.2), one GPD fitting routine and one seed rule throughout.
Corresponding author: Isaac Gwanani (gwananii@gmail.com)
---
1. Contents
File	Description
`Latent Drift (1).ipynb`	The full pipeline (Parts 0–12) with the outputs of the authors' final run, plus three utility cells (software versions, data checksums, Table B1/H1 extraction)
`README.md`	This file
`requirements.txt`	Python packages and the versions used for the paper
The price data are not included (see Section 3).
---
2. Software
The results in the paper were produced with:
Package	Version
Python	3.12.7
numpy	1.26.4
pandas	2.2.2
scipy	1.13.1
matplotlib	3.9.2
The notebook also uses `joblib` (parallel simulations), `yfinance` (data download) and Jupyter/IPython (display). Install with:
```bash
pip install -r requirements.txt
```
Part 11 records the exact versions of your own run in `ldrift_results/manifest.json`.
---
3. Data
Daily closing prices from Yahoo Finance, downloaded with `yfinance`, from 1 January 2005 to 29 December 2023 (Bitcoin from 17 September 2014):
Name in code	Series	Yahoo ticker	Returns	First – last return	Used in
`SP500`	S&P 500 index	`^GSPC`	4,780	2005-01-04 – 2023-12-29	Main analysis
`EURUSD`	EUR/USD exchange rate	`EURUSD=X`	4,926	2005-01-04 – 2023-12-29	Main analysis
`GOLD`	Gold futures (front month)	`GC=F`	4,774	2005-01-04 – 2023-12-29	Main analysis
`WTI`	WTI crude oil futures (front month)	`CL=F`	4,776	2005-01-04 – 2023-12-29	Main analysis
`UST10Y`	10-year US Treasury note futures	`ZN=F`	4,767	2005-01-04 – 2023-12-29	Main analysis
`BTC`	Bitcoin	`BTC-USD`	3,390	2014-09-18 – 2023-12-29	Main analysis
`GLD`	Gold fund	`GLD`	4,780	2005-01-04 – 2023-12-29	Data robustness (Section 5.6)
`USO`	Oil fund	`USO`	4,461	2006-04-11 – 2023-12-29	Data robustness (Section 5.6)
`IEF`	Treasury fund	`IEF`	4,780	2005-01-04 – 2023-12-29	Data robustness (Section 5.6)
Part 1 prints these counts and dates, so you can check them against your own run. WTI uses simple returns, 100 (P<sub>t</sub> − P<sub>t−1</sub>) / |P<sub>t−1</sub>|, because the front-month contract settled at a negative price on 20 April 2020. The other series use 100 × log returns.
Licence. Yahoo Finance data are provided for personal use, so the price files are not redistributed here. Part 0B downloads them for you.
Archived snapshots and checksums. Yahoo revises historical data. Part 0B therefore saves each series once as `data/clean/<NAME>_clean.csv` and never overwrites an existing snapshot, unless you set `FORCE_REPLACE_CLEAN = True`. It writes the SHA-256 checksum of every snapshot to `data/clean/CHECKSUMS.csv`. The checksums of the snapshots used in the paper are:
File	SHA-256
`SP500_clean.csv`	`ec2f485bc8e2ae785a4246ba88e8d5524b3d38c1da3b21ad9ddde7181d113ff2`
`EURUSD_clean.csv`	`d05da6bf8ec016c9df87f350d2d81509548bbed26362926cefadeaa18653c5e1`
`GOLD_clean.csv`	`f4746cb467b46865ca5bd9b8d3b090aa6735bd9550252118985435178e48809b`
`WTI_clean.csv`	`3d1be91bba256bdb30eabac1050ebf2720a079a8d2dfeaa74955df7ca533f54a`
`UST10Y_clean.csv`	`ef788867ed2202c37dbe90cf9f62287f44ff160063d4620a28aa04258957ec94`
`BTC_clean.csv`	`79a65ebd28b618b418142267a21e2a55cb23763872418dce04c3f2f527f88529`
`GLD_clean.csv`	`56207f9591af599a0b780bd7dcb321994c89bb6f1b7ed4bd3fb546c8df81b8ef`
`USO_clean.csv`	`26cbf7a5dcdc3dc984018b59246bf21fb10fcfcb98a9ca10bc02fe807cc004a3`
`IEF_clean.csv`	`8da867b83d829adca6109126781fb044a60a74c5f69f5746972ae01daedc9922`
Each time Part 0B runs, it also compares a fresh Yahoo download with the archive and reports how many days differ. In the authors' final run, no series differed on any archived day.
If your checksums match, you have exactly the data used in the paper. If a file differs, Yahoo has revised that series since the paper's download, and some numbers may differ slightly. The utility cell "SHA-256 of the data files" prints the checksums of the files in `data/clean/`.
---
4. How to run
Install the packages (Section 2) and open the notebook in Jupyter.
Optionally, place your own archived snapshots in `data/clean/` as `<NAME>_clean.csv` (columns `Date`, `close`). Otherwise Part 0B downloads them.
In Part 0, set the run mode:
`QUICK = True`: small bootstrap, draw and replication counts. Use this for a quick check that everything runs (minutes).
`QUICK = False`: the settings used in the paper. The authors' full run took about 55 minutes on a Windows desktop computer. Most of that time is the simulations in Parts 8 and 8B.
Run the cells in order, top to bottom (Part 0 → Part 12). Each part uses results from the parts before it.
Results are written to `ldrift_results/` (Section 6). Part 12 also shows every table and figure inline, in manuscript order, and writes `ldrift_results/report.html`.
Settings in Part 0B:
`RUN_DOWNLOAD = False` skips downloading and only rewrites the checksums.
`FORCE_REPLACE_CLEAN = True` replaces the archived snapshots with a new download. Use it only if you deliberately want to move to new data.
Random numbers. Every random draw uses `rng_for(<labels>)`, a NumPy `SeedSequence` built from the base seed `20240601` and a CRC32 hash of labels naming the task, asset, window and replication. Results therefore do not depend on the order in which parts or parallel tasks run.
---
5. Notebook parts and the manuscript
Part	What it does	Manuscript output
0	Configuration, seed rule, output folders	—
0B	Download, archive and checksum the data	Data Availability Statement; `table_download_log`, `table_data_checksums`
1	Data: one snapshot per series, one date rule, one return rule (simple returns for WTI)	Section 4.1; `table_data_summary`

2	Estimators: GARCH(1,1)-t, Kesten exponent, GPD, Hill	Sections 4.2–4.3
3	Tests: drift test (Fisher, Katz, circular shift), VaR and ES backtests	Sections 4.4–4.5
4–5	Full samples, windows and rolling backtests (GARCH-EVT, unconditional POT, FHS, GARCH-t)	Tables 2, 3, 4, 6, 7, 8, 9 and B1; Section 5.2 out-of-sample results
6	Data robustness with the same functions	Section 5.6
7	Theory tables	Table 1; GJR examples and phase grid (Section 3.3)
8	Simulations	Table 5; size of the drift tests (Section 5.2); Tables C1, D1, E1, E2
8B	Threshold/window sensitivity, Proposition 3 illustration, GJR-GARCH with skewed-t innovations	Table G1 (Appendix G); Table H1 and Figure H1 (Appendix H); Table 10 (Section 5.7)
9	Figures	Figures 2–7 and F1; `tableF_threshold_diagnostics`
10	Consistency checks and the list of exceptions used for the abstract and conclusions	`table_checks`, `table_exceptions`, `table_paper_vs_run`
11	Software manifest, script-to-table map, zip archive	`manifest.json`, `README.md`, zip of the results
12	Viewer: every table and figure in manuscript order	`report.html`
Figure 1 (the three regimes) is a schematic diagram and is not produced by the code.
---
6. Output
```
ldrift_results/
├── tables/          one CSV per table (names as in Section 5 above)
├── figures/         every figure as PNG and PDF
├── manifest.json    software versions, settings, seed rule, data summary
├── README.md        map from manuscript tables/figures to output files and notebook parts
└── report.html      all tables and figures in manuscript order
data/
├── raw/<date>/      full raw downloads with download_meta.json
└── clean/           archived snapshots <NAME>_clean.csv and CHECKSUMS.csv
```
Part 11 also zips `ldrift_results/` into one archive for sharing.
---
7. Citation
If you use this code, please cite the paper above. Citation details will be added on publication.
