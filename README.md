# stock-forecast-pwa

I have a Python code and I want to create a PWA for the same notebook hiding all the backend code and just getting the input as stock name and doing the rest.

Here is the backend code:

# Commented out IPython magic to ensure Python compatibility.
# Install the necessary dependencies
!apt-get install -y libgomp1

# Download and install TA-Lib from the repository
!wget http://prdownloads.sourceforge.net/ta-lib/ta-lib-0.4.0-src.tar.gz
!tar -xzvf ta-lib-0.4.0-src.tar.gz
# %cd ta-lib
!./configure --prefix=/usr
!make
!make install
!pip install TA-Lib
!pip install openpyxl xlrd
!pip install --upgrade yfinance

import yfinance as yf
import pandas as pd
import matplotlib.pyplot as plt
import talib
from sklearn.linear_model import LinearRegression
import numpy as np

# Define the NSE stock symbol and timeframe
stock_symbol_nse = 'TATAMOTORS.NS'  # Replace with the actual NSE stock symbol
start_date = '1991-01-01'

# Fetch stock data from Yahoo Finance
stock_data_nse = yf.download(stock_symbol_nse, start=start_date)

# Calculate technical indicators - for example, moving averages
stock_data_nse['MA50'] = talib.SMA(stock_data_nse['Close'], timeperiod=50)
stock_data_nse['MA200'] = talib.SMA(stock_data_nse['Close'], timeperiod=200)

# Plot stock prices and indicators
plt.figure(figsize=(12, 6))
plt.plot(stock_data_nse['Close'], label='Close Price')
plt.plot(stock_data_nse['MA50'], label='50-day MA')
plt.plot(stock_data_nse['MA200'], label='200-day MA')
plt.title('NSE Stock Price and Moving Averages')
plt.xlabel('Date')
plt.ylabel('Price')
plt.legend()
plt.show()

# Calculate support and resistance levels - you might need more sophisticated methods
today = pd.to_datetime('today')

# 3 months
three_months_ago = today - pd.DateOffset(months=3)
data_3months = stock_data_nse[stock_data_nse.index >= three_months_ago]
support_3months_nse = data_3months['Close'].min()
resistance_3months_nse = data_3months['Close'].max()

# 6 months
six_months_ago = today - pd.DateOffset(months=6)
data_6months = stock_data_nse[stock_data_nse.index >= six_months_ago]
support_6months_nse = data_6months['Close'].min()
resistance_6months_nse = data_6months['Close'].max()

# 1 year
one_year_ago = today - pd.DateOffset(years=1)
data_1year = stock_data_nse[stock_data_nse.index >= one_year_ago]
support_1year_nse = data_1year['Close'].min()
resistance_1year_nse = data_1year['Close'].max()


# Output in tabular format
output_table_nse = pd.DataFrame({
    'Tenure': ['3 months', '6 months', '1 year'],
    'Support': [support_3months_nse ,support_6months_nse, support_1year_nse],
    'Resistance': [resistance_3months_nse, resistance_6months_nse, resistance_1year_nse]
})
print(output_table_nse)

# 6 months forecast
forecast_out_6months = 6*30 # Assuming average 30 trading days per month
# Convert dates to numerical representation (days since the first date)
X_6months = (data_6months.index - data_6months.index[0]).days.values.reshape(-1, 1)
y_6months = data_6months['Close']
lr_6months = LinearRegression()
lr_6months.fit(X_6months, y_6months)
future_dates_6months = pd.date_range(start=data_6months.index[-1], periods=forecast_out_6months + 1, freq='D')[1:]
# Convert future dates to numerical representation as well
future_dates_6months_num = (future_dates_6months - data_6months.index[0]).days.values.reshape(-1, 1)
forecast_6months = lr_6months.predict(future_dates_6months_num)


# 1 year forecast
forecast_out_1year = int(len(data_1year) * 0.5)
# Convert dates to numerical representation (days since the first date)
X_1year = (data_1year.index - data_1year.index[0]).days.values.reshape(-1, 1)
y_1year = data_1year['Close']
lr_1year = LinearRegression()
lr_1year.fit(X_1year, y_1year)
future_dates_1year = pd.date_range(start=data_1year.index[-1], periods=forecast_out_1year + 1, freq='B')[1:]
# Convert future dates to numerical representation as well
future_dates_1year_num = (future_dates_1year - data_1year.index[0]).days.values.reshape(-1, 1)
predictions_1year = lr_1year.predict(future_dates_1year_num)

# 5 year forecast (assuming you have enough data)
five_years_ago = today - pd.DateOffset(years=5)
data_5years = stock_data_nse[stock_data_nse.index >= five_years_ago]
forecast_out_5years = int(len(data_5years) * 0.5)
# Convert dates to numerical representation (days since the first date)
X_5years = (data_5years.index - data_5years.index[0]).days.values.reshape(-1, 1)
y_5years = data_5years['Close']
lr_5years = LinearRegression()
lr_5years.fit(X_5years, y_5years)
future_dates_5years = pd.date_range(start=data_5years.index[-1], periods=forecast_out_5years + 1, freq='B')[1:]
# Convert future dates to numerical representation as well
future_dates_5years_num = (future_dates_5years - data_5years.index[0]).days.values.reshape(-1, 1)
predictions_5years = lr_5years.predict(future_dates_5years_num)

# Plotting
plt.figure(figsize=(14, 7))
plt.plot(stock_data_nse['Close'], label='Historical Close Price')
plt.plot(future_dates_6months, forecast_6months, label='6-Month Forecast') # Use forecast_6months instead of predictions_6months
plt.plot(future_dates_1year, predictions_1year, label='1-Year Forecast')
plt.plot(future_dates_5years, predictions_5years, label='5-Year Forecast')
plt.xlabel('Date')
plt.ylabel('Price')
plt.title('Stock Price Forecast')
plt.legend()
plt.show()




## Collaborate with GPT Engineer

This is a [gptengineer.app](https://gptengineer.app)-synced repository 🌟🤖

Changes made via gptengineer.app will be committed to this repo.

If you clone this repo and push changes, you will have them reflected in the GPT Engineer UI.

## Tech stack

This project is built with .

- Vite
- React
- shadcn-ui
- Tailwind CSS

## Setup

```sh
git clone https://github.com/GPT-Engineer-App/stock-forecast-pwa.git
cd stock-forecast-pwa
npm i
```

```sh
npm run dev
```

This will run a dev server with auto reloading and an instant preview.

## Requirements

- Node.js & npm - [install with nvm](https://github.com/nvm-sh/nvm#installing-and-updating)
