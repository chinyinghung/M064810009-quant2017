# HW07

策略績效:



Total Returns
81.84%
Benchmark Returns
33.41%
Alpha
0.24
Beta
-0.02
Sharpe
0.95
Sortino
1.65
Volatility
0.25
Max Drawdown
-27.49%

From 2014-02-02 to 2017-01-01 with $10,000,000 initial capital

程式碼:

import talib
import numpy as np
import pandas as pd

# Setup our variables
def initialize(context):
    context.security = [sid(13835),sid(5010),sid(24)]
    schedule_function(rebalance, date_rule=date_rules.every_day())

def rebalance(context, data):
    for stock in context.security:
        hist_price = data.history(
            stock,
            fields='price',
            bar_count=30,
            frequency='1d'
         )
        hist_volume = data.history(
            stock,
            fields='volume',
            bar_count=60,
            frequency='1d'
         )
    # Create 10-day and 30-day trailing windows.
        prices_10 = hist_price[-10:]
        prices_30 = hist_price
    # 10-day and 30-day simple moving average (SMA)
        sma_10 = prices_10.mean()
        sma_30 = prices_30.mean()
    # calculate average volume
        average_volume = hist_volume.mean()
    # acquire current price.
        current_volume = data.current(stock, 'volume')
        # If our stock is currently listed on a major exchange
        if data.can_trade(stock):
        # If the current volume is 1% above the 60 day average price,
        # we open a long position. If the current volume is below the
        # average volume, then we want to close our position to 0 shares.
            if current_volume > (1.01 * average_volume):
            # Place the buy order (positive means buy, negative means sell)
                order_target_percent(stock, 1)
                log.info("Buying %s" % (stock.symbol))
            elif current_volume < average_volume:
            # Sell all of our shares by setting the target position to zero
                order_target_percent(stock, 0)
                log.info("Selling %s" % (stock.symbol))
            # If the sma_10 is above sma_30,
            # we open a long position. If the sma_10 is below the
            # sma_30, then we want to close our position to 0 shares.
            if sma_10 > sma_30:
                order_target_percent(stock, 1)
                log.info("Buying %s" % (stock.symbol))
            elif sma_10 < sma_30:
                order_target_percent(stock, 0)
                log.info("Selling %s" % (stock.symbol))
    # Use the record() method to track up to five custom signals.

    record(current_volume=current_volume, average_volume=average_volume)
    record(sma_10=sma_10,sma_30=sma_30)
