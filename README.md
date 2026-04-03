README
Trader Performance vs Market Sentiment — Hyperliquid 2024
Data Science Intern Assignment | Primetrade.ai
Setup
bash
pip install pandas numpy matplotlib jupyter
Run
bash
jupyter notebook analysis.ipynb
Run all cells top-to-bottom. No external API calls. All outputs saved to output/.

Repository Structure
text
├── analysis.ipynb                        ← Main notebook
├── historical_data.csv                   ← Hyperliquid trade data
├── fear_greed_index.csv                  ← Bitcoin F&G index
├── README.md                             ← This file
└── output/
    ├── chart1_sentiment_performance.png  ← PnL, win rate, L/S by sentiment
    ├── chart2_transitions_score.png      ← Transition days + score scatter
    └── chart3_segments_backtest.png      ← Trader segments + backtest
Methodology
Datasets used:

historical_data.csv — 211,224 trade-level rows from 31 Hyperliquid accounts (Jan–Dec 2024)

fear_greed_index.csv — Daily Bitcoin Fear/Greed score (0–100) with classification labels

Cleaning decisions:

Parsed Timestamp IST using dayfirst=True (DD-MM-YYYY format)

Filtered Fear/Greed data to 2024 date range before merging

Critical: Only the 16,754 closing trades (Direction contains "Close") carry realized PnL.
All performance metrics were computed exclusively on closing trades to avoid dilution.

Sentiment collapsed from 5 classes → 3 (Fear, Neutral, Greed) for statistical power

Inner join on date yielded 52,491 matched rows across 340 unique dates

Metrics built:

Daily PnL per trader (sum of closing trade PnL)

Win rate per trader per day (% of closing trades with PnL > 0)

Long/Short ratio (BUY count / SELL count per day)

Trade frequency (total trades per account per day)

Sentiment transition flag (days where classification changed vs prior day)

Trader segments: Consistent Winner / Consistent Loser / Inconsistent
