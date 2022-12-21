# 为什么 NSEpy 返回空数据帧

> 原文：<https://dev.to/casualcoder/why-does-nsepy-returns-empty-dataframe-1c7f>

# 挫折

你正在下载 100 只股票的数据，突然你意识到有一半的数据是空的。刚刚发生的事情，NSE 的网站大概检测到你的 IP 请求太多，现在把你屏蔽了。但问题是你真的每次都需要下载同样的数据吗？

# 典型分析周期

让我们假设您每周都在对过去一个月的数据进行分析-

```
from datetime import date,timedelta
from nsepy import get_history

def download_data_for_month():
    end_day = date.today()
    start_day = end_day - timedelta(30)
    stock_price_df = get_history(symbol="SBIN", start=start_day, end= end_day)
    return stock_price_df

def do_some_analysis(df):
    """ Some Cool Analysis """

df = download_data_for_month()
do_some_analysis(df) 
```

# 上面的例子怎么了——

*   脚本会在您每次运行它时下载数据，在开发周期中，您可能会频繁地更改代码，一天甚至一小时多次。为什么要在每次迭代中下载数据呢？它不仅会降低你的开发效率，还会加载 NSE 的网站。
*   假设您每周都在运行脚本，增量数据更改仅持续 7 天，但您仍在请求整整 30 天的数据。

# 我们能改变什么

有单独的脚本用于下载和分析

download_data.py

```
def download_data_for_month():
    end_day = date.today()
    start_day = end_day - timedelta(30)
    stock_price_df = get_history(symbol="SBIN", start=start_day, end= end_day)
    return stock_price_df

df = download_data_for_month()
df.to_pickle('SBIN.pkl') 
```

analysis.py

```
import pandas as pd
def do_some_analysis(df):
    """ Some Cool Analysis """

df = pd.read_pickle('SBIN.pkl')
do_some_analysis(df) 
```