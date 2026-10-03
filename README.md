# Algo-Trading

A collection of algorithmic trading experiments in Python and Jupyter: trading bots, backtests and a
stock screener for crypto (Binance), forex (OANDA), stocks and options (Interactive Brokers) and
MetaTrader 5.

> **Not financial advice.** These notebooks are learning projects. Backtest results are not
> predictions, and running a bot against a live account can lose real money. Use testnet or
> practice accounts.

## Projects

| Folder | What it does | Broker / data |
|---|---|---|
| `Crypto Bot + Backtest/` | RSI and EMA strategy bots with backtests (fees included), monthly win-rate and profit stats, and balance charts | Binance |
| `First Bot Challenge (Newbie)/` | A first Supertrend-based trading bot on the Binance testnet, built with Sofia Sorto | Binance testnet |
| `Cleaning Template/` | Template for pulling Binance price data and cleaning it (types, missing values, duplicates) | Binance testnet |
| `Forex (OANDA) Bot/` | Downloads historical candles and runs an RSI bot on 1-minute EUR/USD data | OANDA |
| `IBKR/` | Connecting to Interactive Brokers, plus a stock screener that saves results to SQLite | Interactive Brokers, Yahoo Finance |
| `Opening_Range_Break_Out/` | Opening range breakout bot for options with take-profit and stop-loss | Interactive Brokers |
| `MT5/` | RSI strategy bot and backtest for MetaTrader 5 | MetaTrader 5 (Windows) |

## Getting started

1. Install Python 3.10 or newer, then install the dependencies:

   ```bash
   pip install -r requirements.txt
   ```

   TA-Lib needs its C library first ([instructions](https://ta-lib.org/install/)).
   MetaTrader 5 only runs on Windows.

2. Add your API keys. Copy `.env.example` to `.env`, fill in the keys for the broker you want to
   use, and load them before starting Jupyter:

   ```bash
   set -a; source .env; set +a
   jupyter notebook
   ```

   The notebooks read keys from environment variables, so keys never need to be typed into the
   code. `.env` is in `.gitignore` and never gets committed.

3. Open a notebook and run the cells from top to bottom.

For Interactive Brokers, start TWS or IB Gateway with the API enabled, and set `api_ip` in the
notebook (default port 7497 is paper trading).

## License

Licensed under the [Apache License 2.0](LICENSE).
