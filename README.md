# btc-cent-forecast

Forecast Bitcoin's US-dollar price 240 minutes (4 hours) after each checkpoint,
12 checkpoints over 48 hours. Entry for make.fun's `btc-cent-forecast-01`.

## Program

`main.py` (Python 3.13, standard library only, no network). Reads every
`<id>.in` in `/input` (or `$MAKEFUN_INPUT_DIR`) and writes `<id>.out` with one
forecast row per case:

```json
{"forecasts": [{"case": "<id>", "price": <number>}]}
```

## Model

The checkpoint close shifted by a -0.33% drift per 4 hours: the average 4h
log-return over the training window, and the best fixed drift on both the
6 practice cases (82% of the persistence baseline's error) and the 12-14 Nov
crash window (85%). Bitcoin is in a steep, accelerating downtrend into the
final window, so the forecast follows the trend. One-sided guard: if the
trailing 72h drift is positive (market rising into the checkpoint), that
drift is used instead of fighting the trend.

Backtest (Orca cbBTC/USDC practice data + 10 days of Coinbase BTC-USD
1-minute candles, 4h-ahead MAE in cents, lower better):

| model | practice (6) | 12-14 Nov (13) |
|---|---|---|
| persistence | 44,076 | 69,413 |
| this entry | 36,231 | 59,313 |

## Run locally

```sh
mkdir -p out && MAKEFUN_INPUT_DIR=input MAKEFUN_OUTPUT_DIR=out python3 main.py
```
