# Web Scrapers 和 Pen Testers 注意:Slither 现在是一个 PyPI 包！🎉

> 原文：<https://dev.to/kaelscion/attention-web-scrapers-and-pen-testers-slither-is-now-a-pypi-package-pih>

嘿，数据科学、web 自动化、web 抓取和数据聚合的人们。你是否厌倦了购买代理 IP 地址，而这些地址在你的目标网络资产上最多几天就被屏蔽了？您是否还没有自己的解决方案来循环使用 IP 地址和/或用户代理？你喜欢像这样的超级推销吗？在被问了愚蠢的反问后，你会倾向于从 QVC 买东西吗？？！那么我有好消息要告诉你！

玩笑归玩笑，我终于把我的代理 IP 和用户代理循环库 Slither 上传到 PyPI 了！要查看 GitHub 回购协议，请点击[这里](https://github.com/kaelscion/slither)，要查看 PyPI 页面，请点击[这里](https://pypi.org/project/slitherlib/)

仅支持 python 3，**不支持 python 2**。这是我尽自己的绵薄之力，鼓励使用 Python 3 而不是 Python 2。要将它安装到您的下一个项目中，在一个只有 Python-3 的环境中:

`pip install slitherlib`

对于多发行版环境:

`pip3 install slitherlib`

要在您的抓取项目中实际使用该库:

```
 from slitherlib.slither import Snake
from random import choice

import requests

s = Snake()
ip_address = choice(s.ips)
user-agent= choice(s.uas)

headers = {
    "User-Agent": user-agents
}

r = requests.get('https://www.google.com', 
                 proxies={'https': ip_address, 
                          'http': ip_address},
                 headers=headers}) 
```

Enter fullscreen mode Exit fullscreen mode

此时，Slither 从网络上的免费资源中获取 IP 地址和用户代理，并将它们转储到两个变量中，`ips`和`uas`。我们添加新的代理 ip:port 源，因为我们可以找到它们，并尽我们所能验证它们不是由试图窃取 ip 地址信息的黑客运行的。

随着这个项目的发展，我们希望将它构建成一个完整的 web 抓取套件，轻松支持并发和多处理、ROBOTS.txt 支持、webdriver 浏览器自动化、动态鼠标移动和其他好处，让数据收集爱好者收集更多数据，减少 403 和 404 代码！

如果你喜欢，请在 GitHub 上给我们一颗星！我欢迎 bug 报告、特性请求以及您的任何评论或关注，这样我就可以把这个库做到最好！和往常一样，我喜欢合作，所以如果你有改进或想法，请随时打开公关！