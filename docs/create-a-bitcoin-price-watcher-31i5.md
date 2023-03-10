# 创建一个比特币价格观察者

> 原文：<https://dev.to/codesharedot/create-a-bitcoin-price-watcher-31i5>

您可以创建自己的加密货币价格标签。这是学习如何使用 Python 中的 API 的绝佳练习。

coinmarketcap API 是一个免费的 API。您可以直接从脚本中请求价格信息。为了发出请求，我们使用请求模块。

```
#!/usr/bin/python3
import requests

bitcoin_api_url = 'https://api.coinmarketcap.com/v1/ticker/bitcoin/'
response = requests.get(bitcoin_api_url)
response_json = response.json()
print(response_json) 
```

如果您运行该脚本，它会将当前价格和更多信息输出到您的终端。

```
[{'id': 'bitcoin', 'name': 'Bitcoin', 'symbol': 'BTC', 'rank': '1', 'price_usd': '10681.1782936', 'price_btc': '1.0', '24h_volume_usd': '21114044526.6', 'market_cap_usd': '191336753303', 'available_supply': '17913450.0', 'total_supply': '17913450.0', 'max_supply': '21000000.0', 'percent_change_1h': '0.11', 'percent_change_24h': '7.82', 'percent_change_7d': '5.02', 'last_updated': '1567522779'}] 
```

您可以像这样获得当前价格:

```
for coin in response.json():                                        
    print(coin.get("price_usd", "U$S Price not provided")) 
```

这就得出$10616.5048903，需要四舍五入。这是一个字符串，但是您可以像这样转换它:

```
btc_price = float(("{0:.2f}").format(float(price)))             
print("$ " + str(btc_price)) 
```

可能有更好的方法来做这件事，但这确实有效。

### 转换

[![conversion](img/99f665b112b50bd65ade2f015c4afe4f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mDtpMb-q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.pixabay.com/photo/2016/11/23/14/37/blur-1853262_960_720.jpg)

如果你不住在美国，你很有可能使用另一种货币。您可以使用模块 forex_python 来转换汇率。

加载模块:

```
from forex_python.converter import CurrencyRates 
```

然后你可以得到美元对另一种货币的兑换率，如下所示:

```
c = CurrencyRates()                                                 
rate = c.get_rate('USD', 'EUR')                                     
print(rate) 
```

然后将 btc 价格转换为欧元:

```
btc_price_eur = float(("{0:.2f}").format(float(price)*rate))    
print("\u20ac " + str(btc_price_eur)) 
```

你问的这个“\u20ac”是什么？这就是你如何打印欧元符号。这是 unicode 字体代码。

你可以在这里找到 unicode 货币符号列表:
[unicode 货币符号](https://www.compart.com/en/unicode/category/Sc)。

### 通知

如果你在交易，你想得到通知，而不是 24/7 看价格。您可以将 os 模块与 notify-send 应用程序(Linux)配合使用来获取通知。

```
if btc_price_eur > 9000:                                        
   os.system("notify-send trade btc") 
```

你也可以在手机或电子邮件上这样做。要持续观察价格，可以将整个代码放在一个循环中，让它在后台运行。编码快乐！:)

阅读更多信息:

*   [外汇 Python](https://forex-python.readthedocs.io/en/latest/usage.html)
*   [Python JSON 解析](https://pythonbasics.org/json/)
*   [PyQt 通知(跨平台)](https://pythonbasics.org/pyqt-qmessagebox/)
*   [学习 Python](https://pythonbasics.org/)