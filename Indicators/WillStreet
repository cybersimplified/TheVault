import yfinance as yf
import pandas as pd
import pandas_ta as ta
import numpy as np
import matplotlib.pyplot as plt

def willstreet_indicator(df, channel_length=100, channel_mult=1.0, ema_length=200, stoch_k=14, stoch_smooth_k=3, stoch_smooth_d=3):
    # WillStreet Channel
    df['midline'] = ta.linreg(df['Close'], length=channel_length)
    df['dev'] = df['Close'].rolling(window=channel_length).std() * channel_mult
    df['upper'] = df['midline'] + df['dev']
    df['lower'] = df['midline'] - df['dev']
    
    # 200 EMA
    df['ema200'] = ta.ema(df['Close'], length=ema_length)
    
    # Stochastic
    stoch = ta.stoch(df['High'], df['Low'], df['Close'], k=stoch_k, smooth_k=stoch_smooth_k, d=stoch_smooth_d)
    df['stoch_k'] = stoch['STOCHk_14_3_3']
    df['stoch_d'] = stoch['STOCHd_14_3_3']
    
    # Willalert conditions (return booleans for touches/crosses)
    df['touch_lower'] = (df['Close'] <= df['lower']) | (df['Close'].shift(1) > df['lower']) & (df['Close'] <= df['lower'])
    df['touch_upper'] = (df['Close'] >= df['upper']) | (df['Close'].shift(1) < df['upper']) & (df['Close'] >= df['upper'])
    df['cross_mid_up'] = (df['Close'].shift(1) < df['midline']) & (df['Close'] >= df['midline'])
    df['cross_mid_down'] = (df['Close'].shift(1) > df['midline']) & (df['Close'] <= df['midline'])
    
    return df

# Example usage: Fetch SPX500 5m data and apply
symbol = '^GSPC'  # SPX500
data = yf.download(symbol, period='5d', interval='5m')  # Adjust period/interval
data = willstreet_indicator(data)

# Plot (for visualization)
plt.figure(figsize=(14,7))
plt.plot(data['Close'], label='Close', color='black')
plt.plot(data['midline'], label='Midline', color='black', linestyle='--')
plt.plot(data['upper'], label='Upper (Red)', color='red')
plt.plot(data['lower'], label='Lower (Blue)', color='blue')
plt.plot(data['ema200'], label='200 EMA', color='orange')
plt.legend()
plt.title('WillStreet Indicator on SPX500')
plt.show()

# For MTF in bot: Fetch separate DFs for '1m', '5m', '15m' and check Close > EMA200 on each.
