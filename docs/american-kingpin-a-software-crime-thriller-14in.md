# 美国金并:一部软件犯罪惊悚片

> 原文：<https://dev.to/ben/american-kingpin-a-software-crime-thriller-14in>

我刚刚读完《美国金并:追捕丝绸之路背后的犯罪主谋》这本书，作为一本纯粹的娱乐性书籍，对于一个开发网站来说，这是一个非常切题的话题。

这是一本关于丝绸之路的兴衰和它的创始人罗斯·乌布里希特的书。

从最初迫使 Ulbricht 寻求外部帮助的 PHP 技术债务，到计算机取证，到追溯到 Ulbricht 作为证据的堆栈溢出问题——这本书是一部代码惊悚片。

这是书中提到的堆栈溢出问题，如果你好奇的话:

<header>![](img/540c2ba90e5a347bd57c676bb96dfee0.png) [How can I connect to a Tor hidden service using cURL in PHP?](https://stackoverflow.com/questions/15445285/how-can-i-connect-to-a-tor-hidden-service-using-curl-in-php) Mar 16 '13 Comments: Answers: 5[![](img/83f13d376e6608cc602ae93b1cdbae4e.png)450![](img/fd423aaf5fec73c645f97544689ea934.png)](https://stackoverflow.com/questions/15445285/how-can-i-connect-to-a-tor-hidden-service-using-curl-in-php) </header>

我尝试使用以下 PHP 代码连接到 Tor 隐藏服务:

```
$url = 'http://jhiwjjlqpyawmpjx.onion/'
$ch = curl_init()
curl_setopt($ch, CURLOPT_URL, $url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_PROXY, "http://127.0.0.1:9050/");
curl_setopt($ch, CURLOPT_PROXYTYPE, CURLPROXY_SOCKS5);
$output = curl_exec($ch);
$curl_error = curl_error($ch);
curl_close($ch);

print_r($output);
print_r($curl_error); 
```

当我运行它时，我得到以下错误:

…[Open Full Question](https://stackoverflow.com/questions/15445285/how-can-i-connect-to-a-tor-hidden-service-using-curl-in-php)

令人惊讶的是，这在调查中至关重要。

《美国金并》被解读为一个配得上真实犯罪剧的叙事弧线，而且从头到尾都引人入胜。

在这本书和《恶血:硅谷创业公司的秘密和谎言》之间，我非常喜欢阅读这些科技创业公司的罪犯。