# ATESI-SBEC

**Auditable Thermal Energy Storage Intelligence for Smart-Building Energy Prediction and Carbon-Aware Optimization**

This repository contains the complete, reproducible analysis pipeline of ATESI-SBEC, a leakage-safe decision-support framework for thermal energy storage (TES) in a smart building. One notebook takes the public raw data to every table and result figure of the article:

1. **Data** — six years (2016–2021) of one-minute sub-metered records (HVAC, chiller, humidifier, lighting, photovoltaic, battery, net demand), resampled to 209,404 fifteen-minute intervals and split chronologically (80/20).
2. **Causal features and forecasting** — 33 strictly causal features (net-demand lags, sub-meters at *t* and *t*−24 h, trailing rolling statistics, differences, calendar encodings at the target time); one-hour-ahead LightGBM forecaster benchmarked against persistence, seasonal naive, ridge, lasso, elastic net, random forest, XGBoost, ARIMA, LSTM and a Transformer encoder, with Diebold–Mariano tests and a ten-origin rolling backtest.
3. **Leakage checks** — future-perturbation invariance, partition isolation and a preprocessing audit.
4. **Explanation** — LIME local attributions with rank-consistency (Kendall's τ across seeds and refitted models) and measured dose–response checks.
5. **Recourse** — DiCE counterfactuals for demand-reduction requests, restricted to physically admissible directions, the empirical joint operating envelope and observed chiller coupling ratios.
6. **Dispatch** — receding-horizon mixed-integer program with state-of-charge dynamics and bounds, power and ramp limits, charge/discharge exclusivity, time-of-use energy cost, demand charge, carbon cost and throughput penalty; carbon-weight sweep, storage-parameter sensitivity, reference rule-based controllers, peak-cap and peak-aware variants, and robustness to sensor noise, missing data, tariff and carbon-intensity errors.

## Main results (held-out window)

| Quantity | Value |
|---|---|
| One-hour forecast, LightGBM | R² = 0.728, MAE = 2.056 kW, RMSE = 3.616 kW |
| Ten-origin backtest | mean R² = 0.658 (95 % CI 0.577–0.740) |
| Energy-cost change, optimized dispatch (29 days) | −9.76 % to −10.81 % |
| Emission change, optimized dispatch | +0.21 % to −1.27 % |
| Reference rule-based controllers | cost +1.20 % / +1.88 %, emissions +7.71 % / +11.99 % |
| Realized peak, optimized dispatch | +36.7 % (bill including demand charge on realized peak: +5.9 %) |

The economic benefit is limited to energy arbitrage; the carbon outcome is governed by storage losses and is not robust to plausible errors in the carbon-intensity signal. Peak reduction is not obtained by a day-ahead schedule, because realized peaks fall in the regime where the forecast is least informative.

## Repository structure

```
notebooks/ATESI_SBEC_pipeline.ipynb   complete pipeline, Stages 1–16
results/*.json                        outputs of the verification and extended analyses (Stages 10–16)
results/tables/*.csv|json             intermediate tables behind the article's tables (Stages 2–7)
requirements.txt                      software versions used for the reported results
```

## Data

The dataset is not redistributed here. Download the five annual CSV files of the public *Building Energy Dataset* from Kaggle (https://www.kaggle.com/datasets/waqask/building-energy-dataset) and place them in `{WORK}/data/` (default `WORK=/content/atesi`).

## Running

Open the notebook in Google Colab or Jupyter, install the packages of `requirements.txt`, place the data as above and run the cells in order. Stages 1–4a must run before any later stage. Every long-running step prints its progress; the LSTM and Transformer baselines stop automatically once the validation error fails to improve by 0.5 % for two consecutive epochs. On a two-core CPU the complete notebook runs in under one hour.

Results of the tree ensembles depend slightly on library versions; the reported numbers were produced with LightGBM 4.7.0, XGBoost 3.4.1, scikit-learn 1.9.1 and PuLP 2.9.0 (CBC solver).

## License

MIT — see `LICENSE`. The dataset remains subject to the terms of its Kaggle source.
