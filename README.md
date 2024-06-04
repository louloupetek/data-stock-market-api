In this exercise, we are going to play around with the stock market API [iexcloud.io](https://iexcloud.io/).

The goal here is to get comfortable reading API documentation.

---

## Apple stock the last 3 months

Let's find the API documentation page of [iexcloud.io](https://iexcloud.io/)

<details><summary markdown='span'>Solution
</summary>
Documentation pages are often hidden in the footer or in some menu.<br/>
Typing <i>'the_website_name API documentation'</i> in the google search bar is a quick way to find it too.
solution: <a href="https://iexcloud.io/docs">https://iexcloud.io/docs</a>
</details>

### API setup

The endpoints of the API that we want to use are protected **behind a paywall**.

As Le Wagon, we kindly provide you with a **proxy** to that API for you to use _only during the challenge today_. We trust you!

Here's how it works:

1. The API documentation would read like: use `https://api.iex.cloud/v1/data/core/advanced_stats/amzn?token=<your_token>`
1. Copy this URL, and:
    - replace `api.iex.cloud` with `iex.api.lewagon.com`
    - drop the `?token=<your_token>` bit
1. You can [try it now](https://iex.api.lewagon.com/v1/data/core/advanced_stats/amzn)

### Using the API

Now let's find in the IEXCloud API documentation the **URL** for the last 3 months of Apple stock prices.

When you find the URL copy and paste it in a new tab and look at the data you get from the API.
Remember to adapt the URL to use the Le Wagon API!
It should be a JSON looking like this:
<details><summary markdown='span'>Show example
</summary>

```js
[
  {
    "close": 189.99,
    "fclose": 189.99,
    "fhigh": 192.99,
    "flow": 189.11,
    "fopen": 191.44,
    "fvolume": 17236181,
    "high": 192.99,
    "low": 189.11,
    "open": 191.44,
    "priceDate": "2024-05-28",
    "symbol": "AAPL",
    "uclose": 189.99,
    "uhigh": 192.99,
    "ulow": 189.11,
    "uopen": 191.44,
    "uvolume": 17236181,
    "volume": 17236181,
    "id": "HISTORICAL_PRICES",
    "key": "AAPL",
    "subkey": "",
    "date": 1716854400000,
    "updated": 1716970811820.136
  },
  {
    "close": 189.98,
    "fclose": 189.98,
    "fhigh": 190.57,
    "flow": 188.05,
    "fopen": 188.82,
    "fvolume": 12670416,
    "high": 190.57,
    "low": 188.05,
    "open": 188.82,
    ...
  },
// [...]
]
```
</details>
<br>
⚠️ Take your time before reading the solution, finding what we want in an API documentation can usually take **10 to 15 minutes of reading**

<details><summary markdown='span'>Solution
</summary>
You can find the information here in the documentation:
<a href="https://iexcloud.io/docs/core/HISTORICAL_PRICES">https://iexcloud.io/docs/core/HISTORICAL_PRICES</a>
<br>
The URL is:
<pre>
https://iex.api.lewagon.com/v1/data/core/historical_prices/aapl?range=3m
</pre>
</details>

------

## Using API data in pandas

### Setup

For this exercise we will work in a Notebook.

```sh
jupyter notebook
```

Go ahead and **create** a new Python Notebook named `stocks.ipynb` in the `~/code/<user.github_nickname>/{{local_path_to("02-Data-Toolkit/02-Data-Sourcing/01-Stock-Market-API")}}` folder.

Start with the usual imports in the first cell:

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
%matplotlib inline
```

We will reuse the **API URL** for the **last 3 months of Apple stock prices** here.<br>
To make a **call** to the API you can use the following code:

```python
import requests

url = "YOUR_API_URL"
api_data = requests.get(url).json()
```

You can now **create a dataframe** `apple_df` from this data.

<br>
<details><summary markdown='span'>Solution
</summary>
<code>apple_df = pd.DataFrame(api_data)</code>
</details>

With this dataframe we can **plot** the evolution of the stock price.
But before doing that, we need to do 2 things:
- Convert the `date` column to a datetime object
- Set the `date` column as the index

### Converting the date to a datetime object

To do this, you can use `Pandas.to_datetime()`

- **pd.to_datetime** documentation: [http://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.to_datetime.html#pandas.to_datetime](http://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.to_datetime.html#pandas.to_datetime)
- **Format** documentation: [http://strftime.org/](http://strftime.org/)

<details><summary markdown='span'>Solution
</summary>
<code>apple_df['date'] = pd.to_datetime(apple_df['date'], format="%Y-%m-%d")</code>
</details>

### Set the date column as the index

To do this you can use the DataFrame method **set_index**

- documentation: [https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.set_index.html](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.set_index.html)


<details><summary markdown='span'>Solution
</summary>
<code>apple_df = apple_df.set_index('date')</code>
</details>

### Now we can plot 🎉

First let's plot with only the values in the **`close`** column

<details><summary markdown='span'>Solution
</summary>
<code>apple_df['close'].plot()</code>
</details>

Now we can make a plot with the values in the **`open`, `close`, `high`, and `low`** columns

<details><summary markdown='span'>Solution
</summary>
<code>apple_df[['open', 'close', 'high', 'low']].plot()</code>
</details>

As we can see, our plot is really hard to read. We can improve its readability with the `figsize` argument of the `plot()` method.
- documentation: [https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.plot.html](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.plot.html)

<details><summary markdown='span'>Solution
</summary>
<code>apple_df[['open', 'close', 'high', 'low']].plot(figsize=(12,4))</code>
</details>

### Test your code!

Add and run the following cell to your notebook:

```python
from nbresult import ChallengeResult

result = ChallengeResult('apple',
    index_name=apple_df.index.name,
    index_type=apple_df.index.dtype,
    columns=apple_df.columns
)
result.write()
print(result.check())
```

You can `commit` and `push` your code :rocket:

---

## Back to the API

Let's find out what kind of data we can get from this API 🕵️‍♂️

### What is the URL to find:

1. Amazon's stock prices since last year?
2. Meta's (Facebook) market cap?
3. Apple's research and development spendings quarterly?
4. The most recent news about Tesla?
5. The performance of the 'Energy' sector?

Hint: Sometimes you will need a URL that will give you a larger response, containing the data we are actually looking for.

<details><summary markdown='span'>All Solutions
</summary>
<ol>
    <li><code>https://iex.api.lewagon.com/v1/data/core/historical_prices/amzn?range=1y</code></li>
    <li><code>https://iex.api.lewagon.com/v1/data/core/advanced_stats/meta</code></li>
    <li><code>https://iex.api.lewagon.com/v1/data/core/income/AAPL/quarterly</code></li>
    <li><code>https://iex.api.lewagon.com/v1/data/core/news/tsla?last=1</code></li>
    <li><code>https://iex.api.lewagon.com/v1/data/core/sector_performance/market</code></li>
</ol>
</details>

---

:bulb: Don't forget to **push your code to GitHub**

## (Optional) Plotting  _multiple_  line charts

We'd like to **compare** the evolution of the GAFA stocks (Google, Apple, Meta, Amazon) by plotting them on the _same_ chart.
Reuse the code from above to build a dataframe with one column per stock and keeping the dates as the index.
Maybe you can use some normalization technique at `t = 0` to better compare the relative performance of each stock!
