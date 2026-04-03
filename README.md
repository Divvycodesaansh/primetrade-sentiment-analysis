# Trader Performance vs Market Sentiment — Hyperliquid (2024)
**Primetrade.ai · Data Science Intern Assignment**

**Author:** Divyaansh Monga

## Setup & Run

```bash
pip install pandas numpy matplotlib jupyter
jupyter notebook analysis.ipynb
```

Run all cells top-to-bottom. No internet required. Charts are auto-saved to the `output/` folder.

## Repository Structure

```text
├── analysis.ipynb
├── historical_data.csv
├── fear_greed_index.csv
├── README.md
└── output/
    ├── chart1_sentiment_performance.png
    ├── chart2_transitions_score.png
    └── chart3_segments_backtest.png
```

## Methodology

Two datasets were used:
- **Hyperliquid trades:** 211,224 trade records from 31 accounts (Jan–Dec 2024)
- **Market sentiment:** Daily Bitcoin Fear & Greed Index (score 0–100 + classification label)

Dates were parsed from the IST timestamp (`dayfirst=True`) and merged on date.

### Critical cleaning decision
Only **closing trades** were used for realized performance:
- **16,754** of **211,224** rows are closing trades where `Direction` contains the word **"Close"**.
- These are the only rows with realized PnL.
- All performance metrics were computed exclusively on closing trades to avoid diluting averages and misrepresenting win rates.

### Sentiment classes
Sentiment was collapsed from 5 classes to 3 for statistical power:
- **Fear** / **Neutral** / **Greed**

The merge yielded **52,491** matched rows across **340** dates.

### Metrics built
- Daily PnL per trader (sum of closing trade PnL)
- Win rate per trader per day (% of closing trades with PnL > 0)
- Long/Short ratio (BUY count ÷ SELL count per day)
- Trade frequency (total trades per account per day)
- Sentiment transition flag (days where classification changed vs prior day)
- Trader segments — Consistent Winner, Consistent Loser, Inconsistent

### Note on bias (2024 regime)
2024 was a Bitcoin bull year:
- **263 / 365** days were classified as **Greed** or **Extreme Greed**.
- Fear-day findings are based on only **59** days and have lower statistical power.

## Methodology Summary
- Critical cleaning: only closing trades (`Direction` contains "Close") used for PnL & win rate (**~16,754 rows**)
- Merged on date with 2024 Fear & Greed Index (inner join → **52,491 matched rows**)
- Collapsed sentiment into 3 categories: Fear / Neutral / Greed
- Built daily per-trader metrics: PnL, win rate, trade frequency, L/S ratio, size
- Detected sentiment transition days and created trader segments
- Used both classification labels and continuous F&G score (0–100)

## Key Insights
- Fear days have **82% win rate** but **average -$2,743 PnL** → classic fat-tail risk
- Sentiment transition days are the real danger → **median PnL drops 14%** vs sustained days
- Extreme Greed causes severe overtrading → **68 trades/day** vs **15** on Fear days, yet lower PnL than Neutral
- Only **Consistent Winners** (high win rate + positive total PnL) perform well across all regimes

## Strategy Recommendations (Back-tested on 2024 Data)

### Rule 1 — Fear Protection Rule
When Fear & Greed score **< 35**:
- Cap new position size at **40%** of the **7-day average**
- Reduces the 5 worst single-day losses by ~**60%** (total saving **~$284,000**)

### Rule 2 — Greed Profit-Lock Rule
When Fear & Greed score **> 80**:
- Stop opening new positions after **+2% daily PnL** is reached
- Prevents FOMO chasing and late-session give-back during euphoric markets

These two simple, robust rules would have significantly improved risk-adjusted returns in 2024 while requiring zero lookahead or complex logic.