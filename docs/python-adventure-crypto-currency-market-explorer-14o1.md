# Python 冒险:加密货币市场探索者

> 原文：<https://dev.to/codesharedot/python-adventure-crypto-currency-market-explorer-14o1>

你可能听说过 crypto-currency，alt-coins 和其他东西。你从哪里得到这些加密硬币的？

[Python](https://pythonbasics.org/) 可以帮忙。获取一种货币的市场数据，然后过滤数据并显示交易它们的交易所。

## 取数据

所以我们可以用 Python 代码来实现。首先导入几个模块，并询问用户他们想要什么硬币:

```
#!/usr/bin/python3
import requests                                                                                                                                
import json                                                                                                                                    
import os                                                                                                                                      

coin = input("Coin: ") 
```

然后我们获取 JSON 格式的货币数据

```
#!/usr/bin/python3
api_url = "https://coinmarketbook.cc/api/ticker/" + coin                                                                               
response = requests.get(api_url)                                                                                                       
response_json = response.json()                                                                                                                
print(response_json) 
```

[![](img/819b8b4050f890620720538c19b0a620.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RHQWJwEe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252FvpawfuA6LnYeQ%252Fgiphy.gif%26f%3D1%26nofb%3D1)

## 过滤数据

数据具有以下格式:

```
{'symbol': 'ETH', 'current_price': '218.94323290', 'bid': '484.52353643', 'ask': '0.00000000', 'volume_24h': '9692178886.24000000', 'market_cap': '23606418360.00000000', 'buy_order': '75703291.01651694', 'created_at': '2018-09-25T18:54:35.711Z', 'updated_at': '2019-09-20T14:09:35.216Z', 'sell_order': '0.00000000', 'name': 'Ethereum', 'markets': ['bitmex:ETH/USD', 'bitmex:ETHU19', 'binance:ETH/BTC', 'binance:ETH/USDT', 'binance:ETH/TUSD', 'binance:ETH/PAX', 'binance:ETH/USDC', 'bithumb:ETH/KRW', 'bitfinex:ETH/USD', 'bitfinex:ETH/BTC', 'bitfinex:ETH/EUR', 'bitfinex:ETH/JPY', 'bitfinex:ETH/GBP', 'bitfinex:ETH/USDT', 'bitfinex:ETH/F0:', 'okex:ETH/USDT', 'okex:ETH/USD', 'okex:ETH/CNY', 'okex:ETH/USDK', 'okex:ETH/BTC', 'huobipro:ETH/USDT', 'huobipro:ETH/BTC', 'huobipro:ETH/HUSD', 'bittrex:ETH/BTC', 'bittrex:ETH/USDT', 'bittrex:ETH/USD', 'poloniex:ETH/BTC', 'poloniex:ETH/USDT', 'poloniex:ETH/USDC', 'kucoin2:ETH/PAX', 'kucoin2:ETH/USDC', 'kucoin2:ETH/TUSD', 'kucoin2:ETH/USDT', 'kucoin2:ETH/BTC', 'kucoin2:ETH/DAI', 'zb:ETH/USDT', 'zb:ETH/QC', 'zb:ETH/BTC', 'lbank:ETH/USDT', 'lbank:ETH/BTC', 'exx:ETH/USDT', 'exx:ETH/BTC', 'exx:ETH/CNYT', 'upbit:ETH/KRW', 'upbit:ETH/BTC', 'upbit:ETH/USDT', 'bitforex:ETH/USDT', 'bitforex:ETH/BTC', 'bitforex:ETH/TUSD', 'hitbtc2:ETH/BTC', 'hitbtc2:ETH/USDT', 'hitbtc2:ETH/TUSD', 'hitbtc2:ETH/DAI', 'hitbtc2:ETH/EURS', 'hitbtc2:ETH/GUSD', 'hitbtc2:ETH/PAX', 'hitbtc2:ETH/USDC', 'hitbtc2:ETH/EOSDT', 'hitbtc2:ETH/Bitcoin Cash', 'liquid:ETH/EUR', 'liquid:ETH/USDC', 'liquid:ETH/GUSD', 'liquid:ETH/AUD', 'liquid:ETH/BTC', 'liquid:ETH/ANCT', 'liquid:ETH/PHP', 'liquid:ETH/IDR', 'liquid:ETH/JPY', 'liquid:ETH/USD', 'liquid:ETH/HKD', 'liquid:ETH/SGD', 'liquid:ETH/DAI'], 'asset_id': 'ethereum', 'highest_volume_market': 'binance'} 
```

JSON 数据中的一个字段是可用市场，我们可以这样解析它:

```
#!/usr/bin/python3
print(response_json['markets'])                                                                                                                

for market in response_json['markets']:                                                                                                        
    if "EUR" in market:                                                                                                                        
        print(market) 
```

在上面的 if 语句中，我过滤了欧元。他们交易其他货币(美元，英镑等)，这只是一个过滤器。

## 完整代码

下面的代码收集用户输入的硬币的市场。这些模块都是默认安装的，可能请求除外。

```
#!/usr/bin/python3
import requests
import json
import os

coin = input("Coin: ")

api_url = "https://coinmarketbook.cc/api/ticker/" + coin
response = requests.get(api_url)
response_json = response.json()   
#print(response_json)
#print(response_json['markets'])

for market in response_json['markets']:
    if "EUR" in market:
        print(market) 
```

相关链接:

*   [Coin marketbook，加密货币数据的来源](https://coinmarketbook.cc/)
*   [学习 Python 编程](https://pythonbasics.org/)
*   [github 上的代码](https://github.com/codesharedot/coin-market-search/blob/master/market.py)