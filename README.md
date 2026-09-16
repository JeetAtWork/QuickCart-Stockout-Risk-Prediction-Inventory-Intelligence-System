# 🛒 QuickCart Stockout Risk Prediction & Inventory Intelligence System

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue.svg)](https://www.python.org/)
[![Scikit-Learn](https://img.shields.io/badge/Scikit--Learn-Latest-orange.svg)](https://scikit-learn.org/)
[![Pandas](https://img.shields.io/badge/Pandas-Data%20Analysis-green.svg)](https://pandas.pydata.org/)
[![Status](https://img.shields.io/badge/Status-Production%20Ready-success.svg)]()

An end-to-end machine learning pipeline and predictive analytics engine built to forecast stockout risks for QuickCart dark stores. This project ingests, cleans, and merges multi-table relational inventory data to classify store-SKU stockout vulnerabilities into **Safe**, **At-Risk**, and **Imminent** categories, enabling proactive inventory replenishment during high-demand festival seasons.

---

## 📊 Executive Summary & Business Impact

Inventory stockouts lead to lost revenue, decreased customer loyalty, and operational inefficiencies. This system provides supply chain teams with automated risk scoring, achieving **94% overall accuracy** using a tuned Random Forest classifier. 

* **Festival Demand Spikes:** Identified a **2.45x surge** in imminent stockouts during Diwali week (jumping from 9.51% to 23.31%).
* **Supplier Reliability Impact:** Quantified that suppliers with low reliability scores (<0.75) drive imminent stockout rates up to 15.83%, compared to 3.82% for high-reliability vendors ($\ge$0.85).
* **Feature Dominance:** Demonstrated that inventory cover metrics (`days_of_cover`, `days_of_cover_ratio`, and `reorder_gap`) account for over **75%** of predictive decisions.

---

## 📂 Dataset Schema & Architecture

The relational dataset consists of 5 core operational tables representing 12 stores, 60 SKUs, 15 suppliers, 30 events, and **21,600 daily inventory records**:

1. `dim_stores.csv` — Dark store metadata and geographic locations (with city casing standardization).
2. `dim_skus.csv` — Product catalog, category details, and base unit dimensions.
3. `dim_suppliers.csv` — Vendor performance metrics, lead times, and reliability scores (handling missing text entries like `'N/A'`).
4. `dim_events.csv` — Promotional calendars and festival indicators (highlighting the Diwali peak).
5. `fact_inventory_daily.csv` — Daily panel records tracking opening stock, units sold, units demanded, closing stock, and stockout risk labels.

---

## ⚙️ Pipeline Workflow
1. **Data Ingestion & Sanity Checks:** Validates row counts, structural integrity, and target class distributions.
2. **Data-Quality Handling:** Cleans text-based missing values (`'N/A'`) in supplier scores and normalizes city names.
3. **Exploratory Data Analysis (EDA):** Validates operational signals ("Money-Moments") and temporal risk multipliers.
4. **Feature Engineering:** Derives critical supply chain indicators:
   * `reorder_gap = reorder_point - closing_stock`
   * `days_of_cover_ratio = days_of_cover / lead_time_days_expected`
   * Temporal day-of-month and festival flags.
5. **Chronological Train/Test Split:** Enforces a strict time-based split (Training: Oct 1–23; Testing: Oct 24–30 including the Diwali spike) to prevent data leakage across the daily panel.
6. **Model Benchmarking:** Evaluates three distinct classifiers:
   * **Baseline Classifier (Dummy):** Predicts majority class (`Safe` ~62% accuracy).
   * **Multinomial Logistic Regression:** Interpretable linear model (92% accuracy).
   * **Random Forest Classifier:** Non-linear tree ensemble (**94% accuracy**).
7. **Operational Export:** Generates and exports test predictions to `quickcart_stockout_predictions.csv`.

---

## 📈 Model Performance Comparison

| Model | Overall Accuracy | Precision (Imminent) | Recall (Imminent) | F1-Score (Imminent) |
| :--- | :---: | :---: | :---: | :---: |
| **Baseline (Dummy)** | 62.28% | 0.00 | 0.00 | 0.00 |
| **Multinomial Logistic Regression** | 92.14% | 0.84 | 0.75 | 0.79 |
| **Random Forest Classifier** | **94.01%** | **0.89** | **0.70** | **0.78** |

---

## 🛠️ Tech Stack & Requirements

* **Language:** Python 3.10+
* **Environment:** Google Colab / Jupyter Notebook
* **Libraries:** `pandas`, `numpy`, `scikit-learn`, `matplotlib`, `seaborn`

---

## 🚀 Getting Started & Replication

To run this project locally or in Google Colab:

1. Clone the repository:
   ```bash
   git clone [https://github.com/your-username/quickcart-stockout-prediction.git](https://github.com/your-username/quickcart-stockout-prediction.git)
   cd quickcart-stockout-prediction
   Ensure all 5 CSV data files (dim_stores.csv, dim_skus.csv, dim_suppliers.csv, dim_events.csv, fact_inventory_daily.csv) are placed in your working directory.

Open the Jupyter Notebook or execute the pipeline script sequentially cell-by-cell.

Output predictions will be saved automatically to quickcart_stockout_predictions.csv.

💡 Key Recommendations for Inventory Teams
Automate Festival Buffers: Pre-position inventory 3–5 days prior to peak festival weeks to offset the 2.45x demand multiplier.

Vendor SLA Enforcement: Impose stricter lead-time compliance or safety stock mandates for tier-3 suppliers with reliability scores below 0.75.

Dynamic Reorder Thresholds: Program warehouse dispatch systems to trigger replenishment alerts whenever days_of_cover_ratio approaches critical thresholds, regardless of standard reorder points.

Developed for QuickCart Supply Chain Analytics.

**Author: Somyajeet Satapathy**
