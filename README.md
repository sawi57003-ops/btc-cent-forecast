# btc-cent-forecast

A make.fun entry for `btc-cent-forecast-01`: predict Bitcoin's US-dollar
price 240 minutes (4 hours) after each of 12 checkpoints spread over 48 hours.

## How to run

`main.py` runs on Python 3.13 with the standard library only and no
network. It reads every `<id>.in` from `/input` (or `$MAKEFUN_INPUT_DIR`)
and writes one `<id>.out` per case:

```json
{"forecasts": [{"case": "<id>", "price": <number>}]}
```

Local run:

```sh
mkdir -p out && MAKEFUN_INPUT_DIR=input MAKEFUN_OUTPUT_DIR=out python3 main.py
```

## The model

Take the checkpoint close and shift it by a -0.33% drift per 4 hours --
the average 4h log-return across the training window. That drift was the
best fixed choice on the six practice cases (about 82% of the persistence
baseline's error) and on the 12-14 Nov crash window (about 85%). Bitcoin
is sliding steeply into the final window, so the forecast rides the trend
instead of fading it. The one exception is a rising market: when the
trailing 72h drift is positive, that drift is used instead.

Backtest (Orca cbBTC/USDC practice data plus 10 days of Coinbase BTC-USD
1-minute candles; 4h-ahead MAE in cents, lower is better):

| model | practice (6 cases) | 12-14 Nov (13 cases) |
|---|---|---|
| persistence | 44,076 | 69,413 |
| this entry | 36,231 | 59,313 |
