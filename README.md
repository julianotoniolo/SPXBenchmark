**Repository Description (GitHub “short description”)**

> **SPXBenchmark** – Compare a stock’s multi-horizon returns vs. the S\&P 500, compute alpha, beta and delta in one shot.

---

# SPXBenchmark

## Idea in a Nutshell

**SPXBenchmark** is a command-line Python tool that, given any stock ticker, will fetch its adjusted closing prices alongside the S\&P 500 (`^GSPC`), compute total returns over 5 years, 1 year, YTD, 3 months, 1 month, 1 week, and 1 day—and then estimate the stock’s **alpha** and **beta** against the S\&P 500 using a simple OLS regression on daily returns.

## Background

Many investors—and even quants—want a quick, consolidated view of how a ticker has performed relative to the broader market (S\&P 500).

* **Problem**: Manually pulling multiple date-ranges, calculating returns, and running regressions is tedious and error-prone.
* **Frequency**: Every portfolio manager or retail investor faces this when evaluating stocking picking performance.
* **Motivation**: I wanted a reproducible, scriptable way to benchmark any stock in seconds, without clicking through charts.
* **Importance**: Alpha and beta are core metrics in modern portfolio theory; regular benchmarking helps detect underperformance early.

## Data & AI Techniques

* **Data sources**:

  * [Yahoo Finance via `yfinance`](https://github.com/ranaroussi/yfinance) for historical adjusted close prices.
  * S\&P 500 index ticker `^GSPC` from the same source.
* **Techniques**:

  * **Time-series return computation** for multiple horizons.
  * **Ordinary least squares (OLS) regression** (via `statsmodels`) to estimate alpha (intercept) and beta (sensitivity) on daily returns.
  * Although not “deep” AI, regression is a foundational supervised-learning technique.

## How It’s Used

1. **Installation**

   ```bash
   pip install yfinance pandas statsmodels
   ```
2. **Run the script**

   ```bash
   python spxbenchmark.py
   ```
3. **Input**

   * You’ll be prompted for a stock ticker (e.g. `AAPL`).
4. **Output**

   * A Markdown-formatted table of returns for each period, the S\&P 500 return, the delta (stock minus SPX), plus the computed alpha and beta.
5. **Who Benefits**

   * **Individual investors** wanting a quick relative return summary.
   * **Advisors/analysts** needing a reproducible benchmark check before deeper fundamental or technical analysis.

## Challenges & Limitations

* **Data reliability**: Relies on Yahoo Finance; downtime or data gaps will break the script.
* **Survivorship bias**: Only tickers currently trading are fetched—delisted symbols are out of scope.
* **Linear model assumption**: OLS assumes a linear relationship. During market crises, beta may change, and a single-period regression may not capture time-varying risk.
* **No error bars**: Returns and regression assume point estimates; there’s no confidence interval in the table.

## What’s Next

* **Rolling-window betas** (e.g. 6-month beta) to capture changing correlations.
* **Interactive dashboard** with `Streamlit` or `Voila` to visualize equity vs. SPX over time.
* **Extended factors**: Compute exposures to Fama-French factors (size, value, momentum).
* **Batch mode**: Accept a list of tickers to produce a multi-stock benchmarking report in Excel or PDF.

## Acknowledgments

* **YFinance** by Ran Aroussi for free historical data.
* **Statsmodels** for the OLS regression framework.
* Inspiration: the “Alpha and Beta” section in *Modern Portfolio Theory* texts and many online quant tutorials.
* README structure based on GitHub community best practices and the University of Helsinki’s “Building AI” course guidance.

  
        
