# 以太币价格

> 原文：<https://dev.to/codesharedot/ethereum-price-1bob>

你可能听说过比特币，但你知道以太坊吗？不，以太坊不仅仅是另一个基于区块链的硬币。

以太坊是一个开源的、公共的、基于区块链的分布式计算平台和操作系统，具有智能合约功能。

*   **以太是一种令牌，其区块链由以太坊平台**生成。
*   以太网可以在帐户之间转移，并用于补偿参与挖掘节点所执行的计算。
*   以太坊提供了一个**分散式虚拟机**、**以太坊虚拟机(EVM)** ，它可以使用公共节点的国际网络执行脚本

## 价格

我们生活的大部分都围绕着资产、货币和金钱。我们习惯于用价格来衡量一切。那么乙醚的价格是多少呢？

因为它不仅仅是另一个比特币副本，这可能是你投资的东西(这不是财务建议)。但是你怎么知道现在的乙醚价格呢？

你可以用 Python 获取它

```
#!/usr/bin/python3
import requests
import json
from forex_python.converter import CurrencyRates
import os

c = CurrencyRates() 
rate = c.get_rate('USD', 'EUR') 
print(rate)

ethereum_api_url = 'https://api.coinmarketcap.com/v1/ticker/ethereum/'
response = requests.get(ethereum_api_url)
response_json = response.json()
print(response_json)

for coin in response.json():
    price = coin.get("price_usd", "U$S Price not provided")
    coin_price = float(("{0:.2f}").format(float(price)))
    print("$ " + str(coin_price))
    coin_price_eur = float(("{0:.2f}").format(float(price)*rate))   
    print("€ " + str(coin_price_eur)) 
```

参考:[https://gitlab.com/codelivespeed/ethereum-price](https://gitlab.com/codelivespeed/ethereum-price)

这将以美元和欧元输出当前价格。汇率是用 CurrencyRates 模块计算的。

阅读更多信息:

*   [基于以太坊区块链的计算平台和操作系统](https://ethereum.org/)
*   [EVM，以太坊虚拟机](https://github.com/ethereum/wiki/wiki/Ethereum-Virtual-Machine-(EVM)-Awesome-List)
*   [学习 Python](https://pythonbasics.org/)