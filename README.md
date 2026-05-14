# 🤖 Automated WACC Calculator — Python

> A Python script that computes the Weighted Average Cost of Capital (WACC) end-to-end using **live market data** — replacing a workflow analysts typically perform manually across multiple Excel sheets and data sources.

---

## 💡 Intention

WACC is one of the most critical inputs in any DCF valuation. Yet the standard approach — manually pulling stock prices, computing beta in Excel, looking up risk-free rates, and assembling the formula — is time-consuming, prone to human error, and produces a result that is **stale the moment you save the file**.

This script was built to solve that problem. It automates the entire WACC computation pipeline from data fetching to final output, using real-time market data and statistically rigorous beta estimation — producing a defensible, reproducible result in seconds.

---

## 🧠 Approach

The script follows a **bottom-up beta methodology** — the same approach used by professional equity analysts — rather than simply reading raw beta from a financial data terminal. This involves:

- Fetching live historical price data for the target company and a market benchmark
- Running an OLS regression to estimate raw beta
- Applying the **Blume adjustment** to correct for mean-reversion bias in raw beta
- **Unlevering** beta to isolate business risk from financial risk
- **Relevering** beta using the company's actual capital structure
- Feeding the adjusted beta into the **CAPM framework** to derive Cost of Equity
- Combining with an after-tax Cost of Debt to compute the final WACC

---

## ⚙️ Process — Step by Step

### Step 1 — Data Collection
- Fetches historical daily price data for the **target company** and **Nifty 50** (benchmark) using `yfinance`
- User specifies the ticker, date range, and capital structure inputs

### Step 2 — Raw Beta via OLS Regression
- Computes daily log returns for both the stock and the benchmark
- Runs **regression** of stock returns against market returns using `NumPy`
- Slope of the regression line = **raw beta**

### Step 3 — Blume Adjustment
- Raw betas from historical regression tend to drift toward 1.0 over time (mean reversion)
- Applies the **Blume correction**:

```
Adjusted Beta = (0.67 × Raw Beta) + (0.33 × 1.0)
```

This produces a more reliable **forward-looking beta estimate**

### Step 4 — Unlevering & Relevering Beta
- **Unlever** the adjusted beta to remove the effect of the company's financial leverage:

```
Unlevered Beta = Adjusted Beta / (1 + (1 - Tax Rate) × (Debt / Equity))
```

- **Relever** using the company's current capital structure:

```
Relevered Beta = Unlevered Beta × (1 + (1 - Tax Rate) × (Debt / Equity))
```

### Step 5 — Cost of Equity via CAPM
```
Cost of Equity = Risk-Free Rate + Relevered Beta × Equity Risk Premium (ERP)
```

- Risk-Free Rate: 10-year Government of India bond yield
- ERP: 5.90% derived using the **geometric mean methodology** (Damodaran)

### Step 6 — After-Tax Cost of Debt
```
After-Tax Cost of Debt = Pre-Tax Cost of Debt × (1 − Tax Rate)
```

### Step 7 — Final WACC
```
WACC = (Weight of Equity × Cost of Equity) + (Weight of Debt × After-Tax Cost of Debt)
```

---

## ▶️ How to Run

### Prerequisites
```bash
pip install yfinance pandas numpy
```

### Option 1 — Google Colab (Recommended)
Open directly in your browser — no setup required:

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1HcBVc29tIlDNgufFTVV6Yv0D1r59E99E?usp=sharing)

### Option 2 — Jupyter Notebook
```bash
git clone https://github.com/NasimUlJilani/Automated-WACC-Calculator.git
cd Automated-WACC-Calculator
jupyter notebook WACC_Automation.ipynb
```

### Inputs Required
When prompted, enter the following:

| Input | Description | Example |
|---|---|---|
| `ticker` | NSE stock ticker | `HINDUNILVR.NS` |
| `benchmark` | Market index ticker | `^NSEI` |
| `start_date` | Historical data start | `2019-01-01` |
| `end_date` | Historical data end | `2024-12-31` |
| `risk_free_rate` | 10Y G-Sec yield (decimal) | `0.0685` |
| `Rm` | Annual Market returns (historical | `12.95`
| `cost_of_debt` | Pre-tax cost of debt (decimal) | `0.072` |
| `tax_rate` | Effective corporate tax rate | `0.2517` |
| `market_cap` | Market capitalisation (₹ Cr) | `528000` |
| `total_debt` | Total debt (₹ Cr) | `3200` |

### Output
The script prints a clean summary:

```
=== WACC Computation Summary ===
Raw Beta              : 0.8714
Blume-Adjusted Beta   : 0.8499
Unlevered Beta        : 0.8462
Relevered Beta        : 0.9102
Cost of Equity        : 12.07%
After-Tax Cost of Debt: 5.38%
Weight of Equity      : 99.40%
Weight of Debt        : 0.60%
─────────────────────────────────
WACC                  : 12.28%
```

---

## 📊 Results — Applied to HUL

| Metric | Value |
|---|---|
| Raw Beta | 0.8714 |
| Blume-Adjusted Beta | 0.8499 |
| Relevered Beta | 0.9102 |
| Risk-Free Rate | 6.85% |
| Equity Risk Premium | 5.90% |
| Cost of Equity | 12.07% |
| After-Tax Cost of Debt | 5.38% |
| **WACC** | **12.28%** |

This WACC was directly integrated as the discount rate in the HUL DCF valuation model, producing an implied intrinsic value of **₹673** vs a CMP of **₹2,309** at the time of analysis.

---

## 🛠️ Libraries Used

| Library | Purpose |
|---|---|
| `yfinance` | Live market data fetching |
| `pandas` | Data manipulation and return computation |
| `numpy` | regression and matrix operations |

---

## 🔗 Related Project

This script was built as part of a larger equity research project on Hindustan Unilever Limited:

👉 [HUL Equity Research Report](https://github.com/NasimUlJilani/HUL-Financial-Analysis)

---

## 👤 Author

**Nasim Ul Jilani Memon**
CFA L1 Aspirant | NISM Series XV — Research Analyst | NISM Series VIII — Equity Derivatives
Python · Excel · SQL · Power BI

[🔗 LinkedIn](https://www.linkedin.com/in/nasim-ul-jilani-memon) | [✍️ Substack](https://substack.com/@1nfallible) | [📊 HUL Research Repo](https://github.com/NasimUlJilani/HUL-Financial-Analysis)
