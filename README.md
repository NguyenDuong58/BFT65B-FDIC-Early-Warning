# A Big-Data Pipeline for Bank Failure Early Warning (BFT65B Final Report)

End-to-end, reproducible Big Data Engineering pipeline for U.S. Bank Failure Early Warning built on 42 years of public FDIC Call Reports (`1984Q1–2026Q2`, `1,682,616` bank-quarter rows) using **PyArrow**, **Polars**, **Pandas**, **PySpark**, and **scikit-learn**.

---

## 1. Key Project Metadata & Dataset Summary

* **Download Method & Scope:** Resumable REST API ingestion (`https://api.fdic.gov/banks`) across `170` quarters (`19840331` to `20260630`) with explicit PyArrow schema enforcement (`FIN_SCHEMA`) and automated manifest verification (`manifest_financials.json`).
* **Verified Row Counts:**
  * `financials`: **1,682,616** rows across **24,064** distinct insured institutions (`CERT`), **0** duplicates, **0** incomplete quarters.
  * `failures`: **4,117** bank failure records.
  * `institutions`: **27,835** structural charter directory records.
  * **Active Pre-Failure Silver Panel:** **1,677,817** rows (`24,041` banks) after filtering `4,799` post-failure receivership filings (`REPDTE >= FAILDATE`).
  * **Modeling Sample (`<= 2025Q2`):** **1,660,316** rows with **12,459** positive 4-quarter forward failure labels (y=1, base rate `0.7504%`) across **3,216** distinct failed banks.
* **Chronological Split with 4-Quarter Purge Gap (Zero Look-Ahead Bias):**
  * **Development Period (`1984Q1–2008Q4`):** `1,259,614` rows | `10,680` positive labels (y=1, `0.8479%`) | **2,835 failed banks**.
  * **4-Quarter Purge Buffer (`2009Q1–2009Q4`):** `32,842` rows | `670` positive labels (y=1) | **276 failed banks** *(Purged to prevent forward overlap)*.
  * **Out-of-Time Test Period (`2010Q1–2025Q2`):** `367,860` rows | `1,109` positive labels (y=1, `0.3015%`) | **341 failed banks**.

---

## 2. Repository Structure

```text
├── .gitignore                                    # Prevents committing raw/processed Parquet data files
├── README.md                                     # End-to-end execution guide & citations
├── requirements.txt                              # Pinned Python dependencies
├── notebooks/
│   └── main_pipeline.ipynb                       # Complete Google Colab notebook with saved outputs
├── src/
│   ├── p1_acquire.py                             # Part 1: Resumable FDIC API ingestion
│   ├── p1_storage_benchmark.py                   # Part 1: Empirical CSV vs. Parquet benchmark
│   ├── p2_data_quality.py                        # Part 2: Automated data quality audit
│   ├── p2_build_panel.py                         # Part 2: Point-in-time Silver panel
│   ├── p3_polars_vs_pandas_benchmark.py          # Part 3: Polars vs. Pandas benchmark
│   ├── p3_pyspark_repro_and_scaling.py           # Part 3: PySpark reproduction & Catalyst plan
│   ├── p4_feature_pipeline.py                    # Part 4: CAMELS feature pipeline & audit
│   ├── p5_sklearn_models_and_cost_threshold.py   # Part 5: CV, cost threshold & Test eval
│   └── p5_spark_mllib_demo.py                    # Part 5: Distributed PySpark MLlib demo
└── outputs/                                      # Exported benchmark tables, audit CSVs, and plans
```

---

## 3. End-to-End Reproducibility Guide

### Running on Google Colab (Recommended)
Open `notebooks/main_pipeline.ipynb` in Google Colab, mount Google Drive to `/content/drive`, and select `Runtime -> Run all`. All random seeds are fixed (`RANDOM_SEED = 42`).

---

## 4. Academic Integrity & Citations

1. **FDIC Data & Starter Script:**
   * U.S. Federal Deposit Insurance Corporation (FDIC) BankFind Suite API.
   * BFT65B Course Starter Script, National Economics University (NEU), 2026. Extended with PyArrow schema enforcement.
2. **Methodological References:**
   * Cole, R. A., & White, L. J. (2012). Déjà Vu All Over Again: The Causes of U.S. Commercial Bank Failures.
   * De Prado, M. L. (2018). *Advances in Financial Machine Learning*. Wiley (Purged Temporal CV).
3. **AI Assistance Disclosure:**
   * Google Gemini (2026) was consulted for debugging PyArrow/PySpark schema compatibility and refining academic prose. All empirical analyses were independently executed by the author.