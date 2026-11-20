# Customer Lifetime Value (CLV) Prediction Model
**Elevate Labs Data Analyst Internship — Project 2**

---

## 1. Project Overview
Acquiring new customers is significantly more expensive than retaining existing ones. This project builds an end-to-end machine learning pipeline that predicts how much revenue each customer is expected to spend over the next **90 days** based on their historical purchase behavior.

Using these predictions, customers are segmented into four actionable tiers (**Platinum**, **Gold**, **Silver**, and **Bronze**) so marketing and retention teams can allocate budgets where they generate the highest return.

---

## 2. Dataset Links & Description
This project uses the **UCI Online Retail II** transactional dataset (~1 million retail transactions from a UK-based online giftware store between 2009 and 2011).

* **Direct File Download (Auto-downloaded by the notebook):** https://archive.ics.uci.edu/ml/machine-learning-databases/00502/online_retail_II.xlsx
* **Official UCI Repository Page:** https://archive.ics.uci.edu/ml/datasets/online+retail+II
* **Alternative Kaggle Link (CSV format):** https://www.kaggle.com/datasets/mashlyn/online-retail-ii-uci

### Key Dataset Columns
| Column Name | Description |
| :--- | :--- |
| `Invoice` | Unique 6-digit transaction number (codes starting with `C` indicate cancellations). |
| `StockCode` | Unique product/item code. |
| `Description` | Product name. |
| `Quantity` | Number of units purchased per transaction line. |
| `InvoiceDate` | Date and time when the transaction occurred. |
| `Price` | Unit price per item (in GBP). |
| `Customer ID` | Unique 5-digit identifier assigned to each customer. |
| `Country` | Country where the customer resides. |

---

## 3. Tech Stack & Tools Used
* **Programming Language:** Python 3.10+
* **Data Manipulation:** `Pandas`, `NumPy`
* **Machine Learning:** `Scikit-learn` (Random Forest Regressor, train/test split, MAE/RMSE metrics), `XGBoost` (XGBRegressor), `Joblib` (model saving)
* **Data Visualization:** `Matplotlib`, `Seaborn`
* **Reporting & Validation:** Jupyter Notebook / Google Colab, Microsoft Excel

---

## 4. How the Pipeline Works (Step-by-Step)
1. **Data Cleaning & Preprocessing:**
   * Combines both annual sheets (`Year 2009-2010` and `Year 2010-2011`).
   * Removes rows with missing `Customer ID` values and duplicate records.
   * Filters out cancelled orders (`Invoice` starting with `C`) and invalid quantities or prices ($\le 0$).
   * Computes line-item revenue: $\text{TotalSpend} = \text{Quantity} \times \text{Price}$.

2. **Temporal Cutoff Split (Preventing Data Leakage):**
   * Splits transactions at a cutoff date **90 days prior** to the final date in the dataset.
   * **Observation Window (Past History):** Used exclusively to engineer customer behavior features.
   * **Prediction Window (Last 90 Days):** Used to calculate `Actual_LTV` (the target variable).

3. **RFM & Behavioral Feature Engineering:**
   * **Recency:** Days elapsed since the customer's most recent purchase before the cutoff date.
   * **Frequency:** Total count of distinct orders placed.
   * **AOV (Average Order Value):** Mean spend per order ($\text{HistoricalSpend} / \text{Frequency}$).
   * **HistoricalSpend:** Total cumulative revenue from the customer during the observation window.
   * **Tenure:** Days between the customer's first purchase and the cutoff date.
   * **UniqueProducts:** Count of distinct items purchased.
   * **PurchaseVelocity:** Average days between orders ($\text{Tenure} / \text{Frequency}$).

4. **Model Training & Validation:**
   * Applies a $\log(1 + y)$ transformation to stabilize right-skewed customer spending.
   * Trains **Random Forest** and **XGBoost** regression models on an 80/20 train-test split.
   * Evaluates accuracy on back-transformed currency predictions using **MAE** (Mean Absolute Error), **RMSE** (Root Mean Squared Error), and **$R^2$**.

5. **Customer Segmentation & Business Strategy:**
   | Tier | Population Share | Profile | Recommended Marketing Action |
   | :--- | :--- | :--- | :--- |
   | **Platinum (VIP)** | Top 10% | High frequency, high AOV, recent buyers | Dedicated loyalty rewards, early product access, VIP support. |
   | **Gold (Loyal)** | Next 30% | Steady repeat purchasers | Cross-sell bundles and tiered spend incentives. |
   | **Silver (Growth)** | Next 30% | Moderate recency, 1–2 past orders | Automated replenishment reminders and second-order discounts. |
   | **Bronze (Low-Value)** | Bottom 30% | Dormant or single small-basket buyers | Low-cost automated win-back emails; limit paid ad spend. |

---

## 5. How to Run This Project

### Option A: Run on Google Colab (Recommended — No Setup Required)
1. Open [Google Colab](https://colab.research.google.com/).
2. Click **File -> Upload notebook** and select `CLV_Prediction_Model.ipynb`.
3. Click **Runtime -> Run all** from the top menu.
4. The notebook automatically downloads the dataset, trains the models, displays charts, and generates all output files in the left **Files** sidebar.

### Option B: Run Locally in Jupyter Notebook
1. Install the required libraries:
   ```bash
   pip install pandas numpy scikit-learn xgboost matplotlib seaborn openpyxl joblib jupyter
   ```
2. Launch Jupyter Notebook:
   ```bash
   jupyter notebook CLV_Prediction_Model.ipynb
   ```
3. Run all cells from top to bottom.

---

## 6. Project Deliverables Checklist
* `CLV_Prediction_Model.ipynb` — Complete executable Jupyter Notebook.
* `README.md` — Project documentation and setup guide.
* `xgb_clv_model.joblib` — Serialized trained XGBoost regression model.
* `final_ltv_predictions.csv` — Scored customer predictions and tier segments (for Excel review).
* `clv_visualizations.png` — Feature importance and customer segment comparison charts.
* `Elevate_Labs_CLV_Report.pdf` — 1–2 page summary report covering *Introduction, Abstract, Tools Used, Steps Involved in Building the Project,* and *Conclusion*.
