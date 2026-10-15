# Is Bitcoin a Safe Haven Like Gold?

Empirical study of Bitcoin, gold and the S&P 500 on daily data from 2018 to 2025: volatility, time-varying correlations, dynamic betas, and a comparison of GARCH(1,1) with machine-learning models for volatility forecasting.

Individual project, MSc in Finance, HEC Lausanne (course *Data Science and Advanced Programming*). Full ten-page report: [Report.pdf](Report.pdf).

## Key findings

- **Bitcoin is not digital gold.** Its annualised volatility is 64.1%, **3.81 times** that of gold (14.2%). Despite an annualised return of 34% against 10% for gold, its Sharpe ratio is lower (0.49 against 0.69).
- **Its link with equities has changed.** The 30-day rolling correlation between Bitcoin and the S&P 500 rose from **+0.01** in 2018-2019 to **+0.32** in 2020-2021, with an average beta of +0.76 against +0.04 for gold.
- **It fails when protection is needed.** Bitcoin was uncorrelated or negatively correlated with equities 21% of the time (42% for gold), and only **4.3%** of the time when the VIX was above its 75th percentile.

![Rolling correlation and beta](results/outputs/plots/03_correlation_beta.png)

## Method

| Step | What is done |
|---|---|
| Data | Daily prices from Yahoo Finance (`yfinance`): BTC-USD, ^GSPC, GC=F, ^VIX. 1,788 business days after alignment. |
| Features | Log returns, 30-day realised volatility (annualised), 30-day rolling correlation, 60-day rolling beta, lags at 1, 5 and 10 days. |
| Validation | Strict chronological split: training 2018-2021, out-of-sample test 2022-2025 (748 observations). No random shuffling. |
| Volatility models | GARCH(1,1) baseline (`arch`), Random Forest, Gradient Boosting, XGBoost. |
| Regime classification | Logistic regression and tree ensembles predicting whether the BTC/S&P 500 correlation is ≤ 0 five days ahead. |

### Limitations

The machine-learning models receive current realised volatility as an input, while GARCH(1,1) uses returns only, so their lower forecast error reflects a richer information set as much as a better model class. The classification target and its main predictor are rolling windows that overlap heavily, which inflates the AUC. The report therefore separates statistical predictability from economic usefulness.

## Run it

```bash
git clone https://github.com/DylanZermatten/bitcoin-safe-haven.git
cd bitcoin-safe-haven
python -m venv venv
venv\Scripts\activate          # macOS/Linux: source venv/bin/activate
pip install -r requirements.txt
python main.py                 # or open notebooks/Final.ipynb
```

Data is downloaded at runtime; no dataset is stored in the repository. Outputs are written to `outputs/`. The results shown above are saved in `results/outputs/`.

## Repository structure

```text
bitcoin-safe-haven/
├── main.py                     # full pipeline, end to end
├── notebooks/Final.ipynb       # same analysis as a notebook
├── src/                        # the pipeline split by stage
│   ├── 01_data_preparation.py
│   ├── 02_exploratory_analysis.py
│   ├── 03_volatility_forecasting.py
│   ├── 04_safe_haven_classification.py
│   └── 05_conclusions.py
├── results/outputs/            # saved figures, tables and model metrics
├── Report.pdf                  # ten-page report
├── Proposal.md                 # initial project proposal
├── AI_USAGE.md                 # how AI tools were used
└── requirements.txt
```

## Use of AI

AI assistants (mainly Claude) were used for debugging, code review and documentation. See [AI_USAGE.md](AI_USAGE.md).
