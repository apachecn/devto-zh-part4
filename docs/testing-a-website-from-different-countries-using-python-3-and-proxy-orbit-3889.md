# 使用 Python 3 和 Proxy Orbit 测试不同国家的网站

> 原文：<https://dev.to/fprime/testing-a-website-from-different-countries-using-python-3-and-proxy-orbit-3889>

测试来自不同国家的 web 应用程序可能会很棘手。服务的存在使它变得更容易，但它们通常为真正有用的功能付费。在这篇文章中，我将介绍如何使用免费的 web 代理在不同的地区自动测试 web 应用程序。为此，我将使用 Python 3 和代理 Orbit。

首先，我创建了一个非常基本的演示应用程序，它可以检测进来的用户的位置，并返回一个类似下图的页面。

[![](img/81cd2a55f72998491e7cc2710c58b919.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RmKFYP-h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/SAf12NF.png)

标志和文本将根据用户当时使用的 IP 地址而变化。

这方面的代码如下。它使用 Python、Flask 和 geoip2 库。它还要求您有一个名为“ip_country_db.mmdb”的用于 IP 地址查找的 [mmdb](https://maxmind.github.io/MaxMind-DB/) 文件

app.py

```
import geoip2.database as geoip
from flask import Flask, render_template, request

app = Flask(__name__)

@app.route("/")
def index():
    ip_addr = request.remote_addr
    reader = geoip.Reader("ip_country_db.mmdb")
    country = reader.country(ip_addr)
    return render_template("index.html", country=country, name=country.country.names['en'])

if __name__ == "__main__":
    app.run(port=8080, host="0.0.0.0") 
```

index.html

```
<html>
    <head>
        Location Greeter
    </head>
    <body>
        <div id="greeting">
            <img src="https://www.countryflags.io/{{ country.country.iso_code }}/flat/64.png">
            <div id="words">
                Hello {{ name }}!
            </div>
        </div>
    </body>
</html> 
```

现在，开始有趣的事情。为了编写测试，我们将创建
一个 Python 脚本，它加载 web 页面并检查页面上与被测试位置相关的特定文本。

首先，让我们创建一个加载网页的基本 Python 脚本

```
import os                         
import requests    

url = os.getenv("TEST_URL")                                                                                                                                    

resp = requests.get(url)                                                                                                                                          

print(resp.content) 
```

这个脚本将简单地加载一个位于`TEST_URL` env 变量中的网页并打印其内容。

现在编写代码，实际测试网页内容的正确位置名称。

```
import os
import requests

url = os.getenv("TEST_URL")

countries = [
    {"code": "US", "name": "United States"},
    {"code": "GB", "name": "United Kingdom"},
    {"code": "CN", "name": "China"},
]

for country in countries:
    resp = requests.get(url)
    if country['name'] not in resp.content.decode():
        raise Exception(f"{country['name']} not on webpage") 
```

这段代码显然会失败，因为每个请求都来自同一个 IP 地址。为了解决这个问题，我们将使用代理轨道([https://proxyorbit.com](https://proxyorbit.com))。代理轨道是一个旋转代理 API。每个请求都会给我们一个新的代理，我们可以在我们的脚本中使用。我们可以使用 API 查询参数指定代理的位置。

```
import os    
import requests    

url = os.getenv("TEST_URL")    
proxy_orbit_url = "https://api.proxyorbit.com/v1/?token=Iut1LQeCvN7bxHRkplubawI75qGiWxiXrKR1ARflDKA&location={country}"                              
countries = [                                                                                                                                                  
    {"code": "US", "name": "United States"},                                                                                                                   
    {"code": "GB", "name": "United Kingdom"},                                                                                                                  
    {"code": "CN", "name": "China"},                                                                                                                        
]                                                                                                                                                           

for country in countries:                                                                                                                                   
    presp = requests.get(proxy_orbit_url.format(country=country["code"]))                                                                                   
    if presp.status_code != 200:                                                                                                                            
        raise Exception("Could not get proxy")                                                                                                              
    proxy_json = presp.json()    
    pcurl = proxy_json["curl"]    
    resp = requests.get(url, proxies={"http": pcurl})    
    if country["name"] not in resp.content.decode():    
        print(country['name'], "Failed")                        
    else:                                                       
        print(country["name"], "Passed") 
```

我们不需要增加太多来集成代理支持。我们在脚本的开头添加了一行代码，以指定稍后将使用的代理 Orbit API URL。我们还添加了一个`country`字符串变量，稍后将对其进行修改。

在 for 循环中，我们首先向国家代码中的代理 Orbit API 格式发出请求，以获得我们需要的地区的新代理。然后，我们将代理添加到对我们要测试的 URL 的请求中。

然后，当试图用代理加载页面时，该脚本将打印网页上是否找到了国家名称。

通过向`countries`列表添加新的国家字典，可以添加更多的国家。