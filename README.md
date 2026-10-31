# Intraday Systematic Trading Engine with Walk-Forward Validation

A Python backtesting engine for intraday trading on equity indices and commodity futures, built for the Systematic Trading module of the M2 MMMEF (Université Paris 1 Panthéon-Sorbonne / VinUniversity, 2025–2026). The full write-up is in [`report.pdf`](report.pdf).

## Design
- **Data**: 1-minute bars resampled to 15-minute bars to reduce microstructure noise (bid-ask bounce, asynchronous fills).
- **Universe**: Russell 2000, S&P 500, Dow Jones, QQQ, crude oil and gold futures.
- **Signal**: EMA crossover with a deadband, plus optional trend and direction filters. Commodities also get a 200-day regime filter.
- **Risk and execution realism**:
  - trailing stop-loss;
  - minimum number of bars between trades;
  - signal confirmation bars;
  - maximum number of trades per day.
- **Costs**: 1 bp per trade, with P&L reported both gross and net of fees.
- **Calibration**: grid search scored by an objective that penalises overfitting (capped Sharpe plus a return weight). In-sample tuning uses time-series cross-validation (`TimeSeriesSplit`), with a minimum in-sample length.
- **Validation**: strict separation between in-sample and out-of-sample periods with no look-ahead. The engine replays prices day by day.
- **Portfolio**: daily returns per asset, then a weighted portfolio return.

## Results (out-of-sample, portfolio level)
| OOS window | Annualised return | Sharpe | Max drawdown |
|---|---|---|---|
| 1 month | 41.3% | 7.97 | −0.48% |
| 2 months (main) | 23.5% | 4.41 | −0.88% |
| 3 months | 17.3% | 3.37 | −1.12% |

The in-sample Sharpe is only 0.06, and performance falls steadily as the OOS window lengthens. The main lesson of the project is that a single short OOS window gives overly optimistic numbers. These figures come from a few months of data and are not evidence of a deployable strategy. The report discusses regime shifts, overfitting risk and execution realism in detail.

## Running it
Open `systematic_trading_engine.ipynb` and set `BASE` to a folder of Yahoo Finance 1-minute data saved as `.pkl` files, one sub-folder per download date (`Yahoo_1m_*`). The raw data is not redistributed here.

## Author
Abdoulaye Hottmann Bamba · [LinkedIn](https://www.linkedin.com/in/hottmann) · see also [order-flow-tokenisation](https://github.com/Aedificatormagnus22/order-flow-tokenisation)
