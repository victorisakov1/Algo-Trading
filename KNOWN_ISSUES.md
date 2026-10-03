# Known issues

A line-by-line review in October 2026 found the problems below. They are **not fixed**: some change
how a bot trades, and none of the bots can be tested here without a broker. **Do not run any of
these bots on a live account.** Use a testnet or paper account, and read the code first.

The backtests are simplified: no slippage or spread, orders fill at the next candle's open, and
fees are a flat 0.1% per trade. Treat results as illustrations, not predictions.

## Crypto Bot + Backtest

- **Final position:** an open position at the end is valued at the second-to-last candle's close.
- **Position size:** several backtests trade more than the starting balance (for example 0.2 BTC, about $13k, against $10k), so cash goes negative. This is implicit leverage, and a spot "short" has no borrow cost.
- **Balance charts:** `balance_over_time` records cash, not equity, so drawdowns and the balance charts are not true equity curves.
- **Parameter sweeps (cells 9, 25, 27):** the first sweep has no fees. Combinations with an exit RSI above the entry RSI exit on the next bar and are meaningless. The 400-combination sweep is slow because it rebuilds a DataFrame for every combination.
- **File names:** several analysis cells read `btc_data.csv` or `trade_log.csv`, which no cell writes (the files written are `eth_data.csv`, `btc_logs_with_fees.csv` and `eth_logs_with_fees.csv`). The balance chart cell assumes a $10,000 start while the fee backtest starts at $1,000.
- **Combined curves:** the merged BTC/ETH balance charts match rows by nearest timestamp, which can pair a point with a future value.
- **Live bot cell:** it uses the real Binance endpoint (no testnet), changes its state before an order is confirmed, acts on the still-forming candle, sells the full quantity even though the fee was taken in ETH, does not round to the exchange's step size, and is long-only on 15 minutes while the backtest is long and short on 2 hours.

## First Bot Challenge (Newbie)

Uses the Binance **testnet**. The loop has no `sleep`, so it calls the API continuously, trades the still-forming candle (the Supertrend line repaints), enters mid-trend instead of on a flip, has no error handling, and sells the full quantity although the buy fee was taken in BTC. The quantity of 5 BTC is hard-coded, which would be about $300k if pointed at the live endpoint.

## Cleaning Template

Trimming outliers in price levels uses the whole sample, which is look-ahead for time series and removes legitimate trends. `drop_duplicates` ignores the index. The indicators are calculated before the trimming, so the signals are not affected.

## Forex (OANDA) Bot

- Flipping from long to short sells only the long amount, so the account ends flat while the bot believes it is short.
- An order cancelled by the broker still returns HTTP 201, but the bot updates its position anyway.
- The bot ignores positions already open on the account when it starts, and one transient error ends the whole bot.
- Nothing checks that the endpoint is the practice host.
- RSI uses a simple average, not Wilder's, and includes the still-forming candle.
- The first config cell defines variables the later cells never use. The history download fails if a page comes back empty.

## IBKR

- `IB_Connection`: the variable `client_number` holds the port, and the connection is never closed.
- `Stock Screener`: the "30-day average volume" averages about 60 trading days, and "today" is really yesterday because the end date is exclusive. The code threshold (`> 500`) means 6 times the average, but the text says 5 times. The same data is downloaded repeatedly, the final loop has no `sleep`, and two cells are leftover scratch work. Spaces are removed from symbols before saving.

## MT5

- The start time and the buy and sell prices (with stop-loss and take-profit) are calculated once, so the bot keeps ordering at stale prices and never sees new bars.
- Order results are not checked, the position state is not read from the broker, and `positions_get()[0]` can close a position of another symbol.
- `initialize` and `login` results are not checked.
- The backtest uses different parameters from the live bot (RSI 70/30 against 69/60/38/42, no stop-loss or take-profit, 0.2 BTC against 0.01).

## Opening Range Breakout (work in progress)

- The strategy function re-enters every cycle while the price is outside the range. Nothing checks open positions or trades already made today.
- The opening range uses the latest bar instead of the 09:30 bar, and there is no check for market hours.
- Take-profit and stop-loss are priced from the stock price but placed on the option, as two independent orders (not linked), without waiting for the entry to fill.
- The expiry date in the example is in the past, and a comment says 3 lots while the code trades 10 contracts.
