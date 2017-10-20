---
layout: post
title: Creating quantitative equity portfolios with Bloomberg's Python API
---

**Bloomberg Python API**  <br />  <br />  Bloomberg's Python API can be used to retrieve Bloomberg pricing and financial data on tens of thousands of equities at once. Quantitative analysts and portfolio managers can use this data, coupled with individual security weights, to construct large quantitative factor based portfolios.

Use of this API requires a Windows PC, a Bloomberg Terminal license and authorization from Bloomberg to bulk download their data. In the associated python script, I provide the API with a list of Bloomberg Ticker IDs and weights from csv files (works just as well with a database). The script downloads the relevant pricing and fundamental datasets and reconstructs the performance of three factor based portfolios: momentum, value and volatility. 

**Setting Up**  <br />  <br /> 
1. Download the 64 bit version of the [Anaconda2-5.0.0](https://repo.continuum.io/archive/) distribution. Python 2 is required for compatibility with the **tia** library.
2. The **tia** package can be downloaded from <https://github.com/bpsmith/tia> and allows the BBG API to retrieve historical and intraday data into pandas dataframes.
3. Bloomberg's Python 2.7 64 Bit Experimental release Binary installer available here: <https://www.bloomberg.com/professional/support/api-library/>
4. Use your IDE of choice. I prefer Pycharm: <https://www.jetbrains.com/pycharm/download/#section=windows>

You don't have to get the Anaconda distribution, but it does come with the most useful Python libraries pre-installed.

**Installation**  <br />  <br /> 
1. Ensure you have Python 2.7 installed or install the Anaconda2 distribution and confirm your IDE is using it as the interpreter. In PyCharm this is found in Settings > Project Interpreter > Anaconda2\python.exe
2. Download and run the BBG Python API from the binary installer
3. Install tia from the windows command prompt: **pip install -index-url=http://pypi.python.org/simple/ --trusted-host pypi.python.org tia**

a. **pip** is a native installer which comes bundled with Anaconda2
b. <pypi.python.org> stores python packages
c. **trusted-host** prevents the pip installer checking for SSL security certificates which may prevent downloads
d. Try this command instead if 3 doesn't work: **pip install --trusted-host pypi.python.org tia**


Transportation companies such as [Uber](http://www.uber.com), hire Fleetrisk to administer tests, similar to computerized hazard perception tests, to rank drivers by cognitive, empathy and personality traits. These factors align with the [Big Five](https://en.wikipedia.org/wiki/Big_Five_personality_traits) personality model and provides Fleetrisk with some understanding of the psychological makeup of potential drivers. 

Fleetrisk then fits these driver's vehicles with sensors to monitor every speeding, accelerating, braking and cornering event and the speed at which these events occur. Drivers receive no real time feedback and over time will revert to normal driving behavior.

The longer people drive, the greater the likelihood of an accident occuring. Fleetrisk records each accident claim made by every driver being monitored. Our sample contains the date, severity and cost of each accident among other variables. Whether the driver was at fault or not, is not recorded.

*Figure 1: Correlations between 23 driver personality factors and having an accident*

![_config.yml]({{ site.baseurl }}/images/accidentcorrmat.png)

While NDAs prevent me from expliclty naming the factors, note the unanimously low correlations between all factors and whether a driver will have an accident. This does not bode well for Logistic Classification models relying on a linear combination of one or more of these personality traits. 

*Figure 2: OLS reveals most personality features are not significant*

![_config.yml]({{ site.baseurl }}/images/accident_OLS.png)

This is corroborated by a simple OLS regression model that uses whether a driver had an accident or not as the target and the personality factors as its features. I could go down the non-linear modeling route, but this risks introducing more complexity into our analysis. Logistic regression is preferred because its results are easier to interpret for a client than (say) a boosted decision tree or a neural network.

**Time for some Data Science**

Are there any relationships between driver personality and driving behavior, specifically how fast a driver goes? The sample contains the number of times each driver speeds. However, I'm reticent to use this feature as is, because the longer someone drives the greater the likelihood they will speed or have an accident.

We're better off standardizing this score by this distance or time driven by each driver. Here I divide the number of times a driver speeds by the total number of hours driven and percentile rank these scores. I then create a Kernel Density Plot against a particular variable of interest.

*Figure 3: Kernel Density Plot of how often drivers speed vs variable of interest*

![_config.yml]({{ site.baseurl }}/images/accident_kde.png)

While NDAs prevent me from revealing the actual variable, common sense can point the reader towards what it might be.

Notice 3 clusters appear:
1. Low speeds and Slow variable of interest
2. Low speeds and Fast variable of interest
3. High speeds and Fast variables of interest

What if I used a K-Means clusterer on these two variables. Would the same clusters appear?

*Figure 4: Rare 4th Cluster visible*

![_config.yml]({{ site.baseurl }}/images/accident_clusters.png)

Interestingly, the K-Means clustering algorithm maps over the 3 clusters above and picks up a 4th cluster, drivers who speed a lot and have low values of the variable of interest.

<style>
.tablelines table, .tablelines td, .tablelines th {
        border: 1px solid black;
        }
</style>

Var of interest | Low Value  | High Value
--------------- | ---------- | ----------
High Speed      |  16.7%     | 12.5%
Low Speed       |  13.9%     | 9.5%
{: .tablelines}

**Takeaways**

1. Drive slower
2. Drivers ranked in the top 50% by this variable of interest should really drive slower

**Cost Function**

Only 13% of all drivers in our sample had an accident. An estimator that predicted all 394 drivers were accident free would have an 87% accuracy. This is a typical example of an imbalanced class problem, and traditional techniques to accomodate this such as upsampling the minority class would end up skewing our results.

Instead, we design our cost function around misclassifying good and bad drivers. The cost to Fleetrisk of advertising for an vetting drivers for transportation companies is $25. The median cost of an accident is $2200. Put another way, if we were to reject a driver because we believed they were going to have an accident, but they were actually a good driver, it would cost Fleetrisk $25. On the other hand if we vetted a driver as being "good" but they then went on to have an accident, the cost to Fleetrisk would be $2200. 

<style>
.tablelines table, .tablelines td, .tablelines th {
        border: 1px solid black;
        }
</style>

Predict   | Actual   | Cost
--------- | -------- | ------
Bad       |  Good    | $25
Good      |  Bad     | $2200
{: .tablelines}

Note the penalty associated with bad drivers in this case is 100 times greater than with good drivers, so we've adjusted for our imbalanced class problem.

With this cost function in place, I use a gamut of linear and non-linear models to determine the average cost per driver to Fleetrisk and calculate the savings per driver from different models. For sake of clarity, I present three results below.


*Figure 5: Savings per driver*

![_config.yml]({{ site.baseurl }}/images/accident_savings.png)


Click [here]() to see the entire notebook.

----
****
