# **ASX Stock Beta Analysis Pipeline**

## **Overview**

This project provides a complete data pipeline to analyse the volatility of stocks listed on the Australian Securities Exchange (ASX).

It automates the process of fetching the latest ASX company directory, downloading historical price data via **yfinance**, and calculating the **Beta** for each stock relative to the market index (S\&P/ASX 200). Finally, it aggregates these metrics to produce industry-weighted volatility benchmarks.

## **Features**

* **Automated Data Scraping**: Fetches the current full list of ASX companies directly from the ASX API.  
* **Bulk Price Download**: Uses yfinance to download 5 years of Adjusted Close data for roughly \~2000 current tickers.  
* **Robust Filtering**: Automatically removes delisted companies or those with insufficient data (\< 1 year of trading).  
* **Financial Metrics**:  
  * Calculates Log Returns.  
  * Computes Annualized Beta using the Covariance method.  
* **Industry Analysis**: Calculates market-cap weighted Betas for specific GICS Industry Groups.  
* **Visualisation**: Generates a horizontal bar chart comparing volatility across industries.

## **Technologies Used**

* **Python 3.x**  
* **yfinance**: For retrieving historical stock market data.  
* **Pandas & NumPy**: For vectorised financial calculations and data manipulation.  
* **Matplotlib**: For plotting the industry analysis chart.  
* **Requests**: For fetching the ASX directory CSV.

## **📦 Installation**

1. Clone the repository:  
```Bash  
   git clone https://github.com/yourusername/asx-beta-analysis.git
```
2. Install the required dependencies:  
```Bash  
   pip install pandas numpy yfinance matplotlib requests
```
## **The Pipeline Explained**

The Jupyter Notebook (asx_returns.ipynb) executes the following logic:

### **1. Fetch ASX Directory**

The script sends a request to the ASX research API to retrieve a CSV containing all currently listed companies, their GICS industry groups, and market capitalization. It cleans this data by removing suspended companies and parsing numeric values.

### **2. Download Market Data**

It constructs ticker symbols by appending .AX to the ASX codes. It also adds ^AXJO (The S\&P/ASX 200 Index) to serve as the market benchmark.
```bash
prices = yf.download(codes, period='5y', auto_adjust=False)['Adj Close']
```

### **3. Calculate Returns & Filter**

* **Log Returns** are calculated for stability: ![][image2]  
* **Data Cleaning**: Tickers with fewer than 252 trading days (1 year) are dropped to ensure statistical significance. Dropped tickers are saved to insufficient\_data\_tickers.csv.

### **4. Compute Beta**

Beta is calculated using the covariance between the stock's returns and the market's returns, divided by the variance of the market.


*  *>1*: Stock is more volatile than the market (aggressive).  
*  *<1*: Stock is less volatile than the market (defensive).

### **5. Industry Weighting**

The code performs an advanced aggregation step. It calculates the **Industry-Weighted Beta**:

1. Sum Market Cap per Industry.  
2. Calculate the weight of each stock within its industry.  
3. Compute the weighted average Beta for the entire industry group.

### **6. Visualisation & Output**

Finally, the script generates a horizontal bar chart (industry\_beta\_chart.png) visualizing which industries are currently the most volatile compared to the market baseline (1.0).

## **Output Files**

Running the notebook generates the following files:

| File Name | Description |
| :---- | :---- |
| stock\_prices.csv | Raw 5-year adjusted close prices for all valid ASX stocks. |
| stocks\_with\_betas\_weights.csv | The master dataset containing Ticker, Name, Industry, Market Cap, and individual Beta. |
| industry\_betas.csv | Aggregated weighted Betas per GICS industry group. |
| industry\_beta\_chart.png | A visualization of industry volatility. |
| insufficient\_data\_tickers.csv | Log of companies removed due to lack of historical data. |

## **Example Visualization**

The notebook produces a chart similar to this, identifying high-volatility sectors (e.g., Technology) vs. low-volatility sectors (e.g., Consumer Staples).

<img width="1200" height="1000" alt="industry_beta_chart" src="https://github.com/user-attachments/assets/eb314d84-724f-4aa7-8fa6-cc1a09aff1f3" />


## **🤝 Contributing**

Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

## **📝 License**

[MIT](https://choosealicense.com/licenses/mit/)
