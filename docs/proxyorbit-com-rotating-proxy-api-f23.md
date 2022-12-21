# ProxyOrbit.com——构建一个旋转代理 API

> 原文：<https://dev.to/fprime/proxyorbit-com-rotating-proxy-api-f23>

# 创建另一个代理服务

有很多服务以不同的方式提供代理。有些是批量提供的，有些是通过 API 提供的，有些只是在他们的网站上免费提供。问题是，这些服务中的许多服务是死的或缓慢的代理，这使得它很难使用，除非你自己验证它们正在工作。

对于我计划的几个项目，我需要一个 API，它可以在我每次请求时提供工作代理。我需要一个非常主动地检查代理的服务，这样没有代理会在池中停留足够长的时间。

从这个代理轨道诞生了。

Proxy Orbit 是一项旨在出色完成一系列任务的服务:

扫描 web 上的开放代理，保存它们，经常检查它们，当发出 API 请求时返回一个随机代理，并允许过滤结果。

# 基本 API 用法

一个基本的 API 请求目前看起来像这样:

[https://API . proxy orbit . com/v1/？token = iut 1 lqecvn 7 bxhrkplubawi 75 qgiwixrkr 1 arfldka](https://api.proxyorbit.com/v1/?token=Iut1LQeCvN7bxHRkplubawI75qGiWxiXrKR1ARflDKA)

它返回一个类似下面的对象

```
{
    anonymous: true,
    cookies: true,
    curl: "http://182.96.147.126:8118",
    get: true,
    ip: "182.96.147.126",
    isp: "No.31,Jin-rong Street",
    lastChecked: 1564551731.418154,
    location: "CN",
    port: 8118,
    post: true,
    protocol: "http",
    rtt: 1.008148431777954,
    ssl: false,
    websites: {
       amazon: false,
       facebook: false,
       google: false,
       instagram: false,
       netflix: false,
       nike: false,
       shopify: false,
       stubhub: false,
       supreme: false,
       ticketmaster: false,
       twitch: false,
       twitter: false,
       youtube: false
    }
} 
```

在过去的一个小时内，已检查了所有返回的代理。

还可以使用`count`查询参数批量返回代理。

[https://API . proxy orbit . com/v1/？token = iut 1 lqecvn 7 bxhrkplubawi 75 qgiwixrkr 1 arfldka&count = 5](https://api.proxyorbit.com/v1/?token=Iut1LQeCvN7bxHRkplubawI75qGiWxiXrKR1ARflDKA&count=5)

上面将在 JSON 数组中返回五个代理对象。

代理也可以由对象中的所有参数过滤。例如

[https://api.proxyorbit.com/v1/?token = iut 1 lqecvn 7 bxhrkplubawi 75 qgiwxixrkr 1 arfldka&亚马逊=真&协议= http&SSL =真](https://api.proxyorbit.com/v1/?token=Iut1LQeCvN7bxHRkplubawI75qGiWxiXrKR1ARflDKA&amazon=true&protocol=http&ssl=true)

将返回可以加载 Amazon.com 和支持 HTTPS 的 HTTP 代理。

# Python 和 NodeJS 中的例子

因为所有东西都是通过一个基本的 REST API 提供的，所以集成到现有代码中应该是微不足道的。下面是在 Python 和 NodeJS 中使用代理轨道的两个例子。脚本请求一个新的代理，并检查代理的 IP。

### Python 代码

```
import requests

resp = requests.get("https://api.proxyorbit.com/v1/?token=Iut1LQeCvN7bxHRkplubawI75qGiWxiXrKR1ARflDKA&ssl=true&protocol=http")
if resp.status_code != 200:
    raise Exception("Token Expired!")

json = resp.json()
curl = json['curl']

resp2 = requests.get("https://api.proxyorbit.com/ip", proxies={"https":curl, "http":curl})
print("Proxy IP is:", resp2.content) 
```

### 节点

```
const request = require("request");

request.get("https://api.proxyorbit.com/v1/?token=Iut1LQeCvN7bxHRkplubawI75qGiWxiXrKR1ARflDKA&ssl=true&protocol=http", (err, resp, body) => {
    if(err) {
        console.log(err)    
    } else if(resp.statusCode != 200) {
        console.log("Token has expired");
    } else {
        json = JSON.parse(body);
        request({
            url:"https://api.proxyorbit.com/ip",
            method:"GET",
            proxy:json.curl
        }, (err, resp, body) => {
            console.log("Proxy IP is:", body);
        })
    }
}); 
```

# 检查池质量

为了测试池的质量，我使用了下面的脚本

```
import requests

total = 100
failed = 0
url = input("URL: ")
for x in range(total):
    resp = requests.get("https://api.proxyorbit.com/v1/?token=Iut1LQeCvN7bxHRkplubawI75qGiWxiXrKR1ARflDKAA&protocols=http&ssl=true").json()
    curl = resp['curl']
    try:
        r = requests.get(url, proxies={"http":curl, "https":curl}, timeout=10)
    except Exception as e:
        print(e)
        print(curl, "failed")
        failed += 1
    else:
        print(r.status_code)
        #print(r.content)
        print(curl)
        if r.status_code != 200:
            failed += 1

error_rate = (failed / total) * 100

print(f"{error_rate}% failed") 
```

该脚本向特定的 URL 发出 100 个请求，并跟踪失败的次数。然后用`# failures / 100 * 100`来计算误差百分比。到目前为止，我对`https://proxyorbit.com`的测试显示大约 3%的代理失败(3 / 100 不工作)。尽管 proxyorbit.com 对屏蔽代理 IP 并不严格。

代理注定会失败，因为它们是开放的。其他人也在使用他们，他们比私人代理服务器更快被禁止。尽管在任何给定的时间都很难在池中获得 100%的工作代理，但是代理检查算法可以确保一旦发现损坏的代理失效，就会将其删除。这通常发生在代理不再工作的几分钟内。

# 结论

Proxy Orbit 将作为目前正在开发的几个其他产品的基础。创作这个肯定很有趣，我希望其他人也能从中找到用处。

至于技术栈 Proxy Orbit 用的是 Python 3，Flask，和 MongoDB。

如果你有兴趣，你可以在 https://proxyorbit.com 查看这个网站。如果你对付费服务不感兴趣，当你注册时，我们每个月免费提供 2000 个 API 请求。

非常感谢您的反馈！

谢谢！