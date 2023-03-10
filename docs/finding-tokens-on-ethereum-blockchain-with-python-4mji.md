# 用 Python 在以太坊区块链寻找代币

> 原文：<https://dev.to/codesharedot/finding-tokens-on-ethereum-blockchain-with-python-4mji>

在加密空间，似乎有很多硬币。你可能知道 99%的数字硬币运行在分布式网络上。每种硬币都有网络吗？

不，事实并非如此。许多所谓的硬币在区块链以太坊运行。这意味着它们实际上是令牌。

那么哪些币有自己的网络呢？目前为止是比特币和以太坊。您可以使用[以太扫描](https://etherscan.io/tokens)查看以太坊上运行的令牌

我们可以用 Python 抓取一个令牌列表吗？是的，我们可以。但是你需要知道 [Python 基础知识](https://pythonbasics.org)

## 美丽的声音

导入一堆模块

```
#!/usr/bin/python3
import time
import os
import json
import requests
from bs4 import BeautifulSoup
import csv
import sys
from time import sleep
from time import gmtime, strftime
import re 
```

然后获取 HTML 数据

```
#!/usr/bin/python3
r  = requests.get("https://etherscan.io/tokens")
data = r.text 
```

然后解析该表并获取第一列。因为列中的文本太多，所以我们不再抓取)标记之后的文本。我们用正则表达式去掉 HTML 标签。

```
#!/usr/bin/python3
soup = BeautifulSoup(data, "html.parser")
table = soup.find('table', attrs={ "class" : "table"})
for row in table.find_all('tr'):
    tag = row.findAll('td')
    if len(tag) >= 7:
        token = tag[1]
        token = re.sub('<[^<]+?>', '', str(token))
        if ")" in token:
            token = token[0:token.index(")")+1]
            print(token) 
```

[![](img/96f36a89a3d787a55a7048209edf8846.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uxMtJ4gI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252F1EuLa4HzCWffO%252Fgiphy.gif%26f%3D1%26nofb%3D1)

这只抓取第 1 页上的硬币，但是你可以通过把它包装在一个函数中从所有的页面中抓取它们。

## 去吧

如果您不想复制和粘贴，您可以直接 git 克隆存储库。Git 是开发人员使用的工具，所以如果你是软件开发人员我推荐安装 git。

在终端中，您可以键入:

```
git clone https://github.com/codesharedot/ethereum-tokens.git 
```

如果不要 git，也可以用下面的链接抓取代码。

相关链接:

*   [github 上的代码](https://github.com/codesharedot/ethereum-tokens/blob/master/tokens.py)
*   [以太扫描. io](https://etherscan.io/tokens)
*   [学习 Python](https://pythonbasics.org)