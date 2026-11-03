# Insurance Claims Analytics

**Why is claim settlement delayed, and where should a 30-day improvement pilot start?**

An end-to-end exploratory data analysis of a synthetic insurance claims dataset, written in Python (pandas, NumPy, Matplotlib, Seaborn). The notebook goes from raw CSV files to a clear recommendation: which region, product and claim type to fix first.

**Author:** Anshuman Behera

---

## Business question

A fictional insurer wants to find out why some claims take too long to settle. The analysis answers one question:

> Where should the 30-day delay-improvement pilot start?

Two rules are followed throughout:

- Pending and rejected claims have no settlement duration, so they are never counted as zero days.
- A claim is called **delayed** when it takes **more than 20 days** to settle. This limit is an assumption and can be changed.

## Dataset

The data is synthetic and meant for practice. Four raw CSV files are used:

| File | Rows | Columns | What it holds |
|---|---|---|---|
| `customers_raw.csv` | 8,000 | 5 | age, gender, region, customer since |
| `policies_raw.csv` | 10,000 | 5 | insurance type, annual premium, coverage |
| `claims_raw.csv` | 15,000 | 7 | claim type, claim amount, status, dates |
| `claim_processing_raw.csv` | 15,000 | 5 | complexity, verification / approval / payment days |

One customer has many policies, one policy has many claims, and each claim has one processing record.

> The CSV files are read from the same folder as the notebook. Place them next to `Insurance_Claims_Analytics.ipynb` before running it.

## Workflow

1. **Load** the four raw tables.
2. **Audit** the data: duplicates, missing values and inconsistent labels.
3. **Clean** each table: fix labels, fill missing values with simple rules and flag imputed rows.
4. **Merge** the tables with left joins so that no claim is lost (15,000 rows before and after).
5. **Flag outliers** per product (Q3 + 1.5 x IQR) without deleting them.
6. **Create metrics:** settlement days, delay flag, stage days, claim as % of coverage, age group, claim size.
7. **Univariate analysis** of age, premium, claim amount, stage days, settlement days, status and complexity.
8. **Bivariate analysis** by product, region, complexity, age, gender, claim amount, and over time.
9. **Multivariate analysis:** region x product, region x complexity, product x complexity, claim size x complexity.
10. **Root cause:** which processing stage causes the delay.
11. **Pilot selection:** rank segments by excess delays.

## Key findings

| Measure | Result |
|---|---|
| Claims analysed | 15,000 |
| Settled / Pending / Rejected | 10,773 / 3,030 / 1,197 |
| Median settlement time | 14 days |
| 90th percentile settlement time | 21 days |
| Settled claims delayed (> 20 days) | 12.1% |

- **Complexity is the main driver.** High-complexity claims take a median of 22 days and 72% are delayed, against 1% to 2% for Low and Medium claims.
- **Health looks slowest and East looks slowest by region,** but much of the product gap is explained by case mix. East stays slower even at the same complexity.
- **Age, gender and claim amount show no meaningful effect** on settlement time.
- **Verification and approval are where the time goes.** Delayed claims spend about 4 more days in verification and 5 more days in approval, and no more in payment.
- **Best pilot segment: South / Health / High complexity** (176 claims, 81.8% delayed, 123 excess delays).

These are associations from a synthetic dataset, not proof of cause. Any change should be measured in the pilot.

## Data quality notes

- No duplicate rows or duplicate IDs in any table.
- Missing values: age 65, gender 80, annual premium 90, claim amount 95, verification days 100. Settlement date is missing for 4,227 open claims, which is expected.
- 688 high-amount claims are flagged as outliers but kept, because large claims can be valid.
- 15 claims are above their coverage amount and should go for a rule review.

## How to run

1. Clone the repository and place the four CSV files in the project folder.
2. Install the requirements:

   ```bash
   pip install pandas numpy matplotlib seaborn jupyter
   ```

3. Open the notebook and run all cells:

   ```bash
   jupyter notebook Insurance_Claims_Analytics.ipynb
   ```

## Project structure

```
.
├── Insurance_Claims_Analytics.ipynb   # full analysis
├── customers_raw.csv
├── policies_raw.csv
├── claims_raw.csv
├── claim_processing_raw.csv
└── README.md
```

## Suggested actions

1. Run a **document checklist pilot** in South / Health / High and measure verification days and delay rate.
2. **Fast-route high-complexity claims** and track P90 verification days.
3. **Track weekly** verification, approval and other waiting time by region and product.
4. **Update customers** with old pending claims and measure complaints and satisfaction.

## Tools

Python, pandas, NumPy, Matplotlib, Seaborn, Jupyter Notebook.
