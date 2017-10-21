---
layout: post
title: Creating quantitative equity portfolios with Bloomberg's Python API
---

**Bloomberg Python API**  <br />  <br />  Bloomberg's Python API can be used to retrieve Bloomberg pricing and financial data on tens of thousands of equities at once. Quantitative analysts and portfolio managers can use this data, coupled with individual security weights, to construct large quantitative factor based portfolios.

Use of this API requires a Windows PC, a Bloomberg Terminal license and authorization from Bloomberg to bulk download their data. In the associated python script, I provide the API with a list of Bloomberg Ticker IDs and weights from csv files (works just as well with a database). The script downloads the relevant pricing and fundamental datasets and reconstructs the performance of three factor based portfolios: momentum, value and volatility. 

**Setting Up**

1. Download the 64 bit version of the [Anaconda2-5.0.0](https://repo.continuum.io/archive/) distribution. Python 2 is required for compatibility with the **tia** library.
2. The **tia** package can be downloaded from <https://github.com/bpsmith/tia> and allows the BBG API to retrieve historical and intraday data into pandas dataframes.
3. Bloomberg's Python 2.7 64 Bit Experimental release Binary installer available here: <https://www.bloomberg.com/professional/support/api-library/>
4. Use your IDE of choice. I prefer Pycharm: <https://www.jetbrains.com/pycharm/download/#section=windows>

You don't have to get the Anaconda distribution, but it does come with the most useful Python libraries pre-installed.

**Installation**

1. Ensure you have Python 2.7 installed or install the Anaconda2 distribution and confirm your IDE is using it as the interpreter. In PyCharm this is found in Settings > Project Interpreter > Anaconda2\python.exe
2. Download and run the BBG Python API from the binary installer
3. Install tia from the windows command prompt: **pip install -index-url=http://pypi.python.org/simple/ --trusted-host pypi.python.org tia**

+ **pip** is a native installer which comes bundled with Anaconda2
+ <pypi.python.org> stores python packages
+ **trusted-host** prevents the pip installer checking for SSL security certificates which may prevent downloads
+ Try this command instead if 3 doesn't work: **pip install --trusted-host pypi.python.org tia**

**Testing**

Once these components are installed, the test code below should work:

```python
import pandas as pd
import tia.bbg.datamgr as dm
mgr = dm.BBgDataManager()
tickers = ['MSFT US EQUITY', 'IBM US EQUITY', 'CSCO US EQUITY']
sids = mgr[tickers]
sids.get_historical('PX_LAST','1/1/2014','10/31/2016')
```

Click [here]() to see the entire notebook.

----
****
