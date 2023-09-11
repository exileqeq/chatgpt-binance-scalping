I asked chatgpt to write me an example of such a combination strategy using the above indicators in python.
## To which chatgpt gave this example:

1. Initialize the cryptocurrency exchange (e.g., Binance)
exchange = ccxt.binance()
2. Define the trading pair and timeframe
symbol = 'BTC/USDT'
timeframe = '5m'
3. Fetch historical data
ohlcv = exchange.fetch_ohlcv(symbol, timeframe)
df = pd.DataFrame(ohlcv, columns=['timestamp', 'open', 'high', 'low', 'close', 'volume'])
4. Calculate moving averages
df['ema10'] = df['close'].ewm(span=10, adjust=False).mean()
df['ema20'] = df['close'].ewm(span=20, adjust=False).mean()
5. Calculate Stochastic Oscillator
def stochastic_oscillator(df, k_period, d_period):
    low_min = df['low'].rolling(window=k_period).min()
    high_max = df['high'].rolling(window=k_period).max()
    df['%K'] = 100 * (df['close'] - low_min) / (high_max - low_min)
    df['%D'] = df['%K'].rolling(window=d_period).mean()

k_period = 14
d_period = 3
stochastic_oscillator(df, k_period, d_period)
5. Calculate MACD
def macd(df, short_period, long_period, signal_period):
    df['ema_short'] = df['close'].ewm(span=short_period, adjust=False).mean()
    df['ema_long'] = df['close'].ewm(span=long_period, adjust=False).mean()
    df['macd'] = df['ema_short'] - df['ema_long']
    df['signal_line'] = df['macd'].ewm(span=signal_period, adjust=False).mean()

short_period = 12
long_period = 26
signal_period = 9
macd(df, short_period, long_period, signal_period)
6. Define entry and exit signals
df['long_entry'] = (df['ema10'] > df['ema20']) & (df['%K'] > df['%D']) & (df['macd'] > df['signal_line'])
df['long_exit'] = df['close'].pct_change() > 0.01  # 1% profit target as exit signal
7. Simulate trades
in_trade = False
for index, row in df.iterrows():
    if row['long_entry'] and not in_trade:
        print(f"Enter long at {row['close']} USD")
        in_trade = True
    elif row['long_exit'] and in_trade:
        print(f"Exit long at {row['close']} USD")
        in_trade = False




import pandas as pd
import numpy as np
import ccxt

# Initialize the cryptocurrency exchange (e.g., Binance)
exchange = ccxt.binance()

# Define the trading pair and timeframe
symbol = 'BTC/USDT'
timeframe = '5m'

# Fetch historical data
ohlcv = exchange.fetch_ohlcv(symbol, timeframe)
df = pd.DataFrame(ohlcv, columns=['timestamp', 'open', 'high', 'low', 'close', 'volume'])

# Calculate moving averages
df['ema10'] = df['close'].ewm(span=10, adjust=False).mean()
df['ema20'] = df['close'].ewm(span=20, adjust=False).mean()

# Calculate Stochastic Oscillator
def stochastic_oscillator(df, k_period, d_period):
    low_min = df['low'].rolling(window=k_period).min()
    high_max = df['high'].rolling(window=k_period).max()
    df['%K'] = 100 * (df['close'] - low_min) / (high_max - low_min)
    df['%D'] = df['%K'].rolling(window=d_period).mean()

k_period = 14
d_period = 3
stochastic_oscillator(df, k_period, d_period)

# Calculate MACD
def macd(df, short_period, long_period, signal_period):
    df['ema_short'] = df['close'].ewm(span=short_period, adjust=False).mean()
    df['ema_long'] = df['close'].ewm(span=long_period, adjust=False).mean()
    df['macd'] = df['ema_short'] - df['ema_long']
    df['signal_line'] = df['macd'].ewm(span=signal_period, adjust=False).mean()

short_period = 12
long_period = 26
signal_period = 9
macd(df, short_period, long_period, signal_period)

# Define entry and exit signals
df['long_entry'] = (df['ema10'] > df['ema20']) & (df['%K'] > df['%D']) & (df['macd'] > df['signal_line'])
df['long_exit'] = df['close'].pct_change() > 0.01  # 1% profit target as exit signal

# Simulate trades
in_trade = False
for index, row in df.iterrows():
    if row['long_entry'] and not in_trade:
        print(f"Enter long at {row['close']} USD")
        in_trade = True
    elif row['long_exit'] and in_trade:
        print(f"Exit long at {row['close']} USD")
        in_trade = False
