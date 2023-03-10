# 如何刮脸书事件的乐趣和利润？

> 原文：<https://dev.to/iduoad/how-to-scrape-facebook-events-for-fun-and-profit-26lo>

**免责声明:这是业余爱好者的作品！！有更好的方法可以做到这一点！！**

上周，我发现了一个关于如何刮脸书事件的帖子，我一直想这么做，所以我决定尝试一下，下面是结果。

## 1-明面上！

为此，您需要:

*   现代浏览器开发工具
*   简单的 HTTP 客户端测试的东西。
*   JS/Browser HTTP 客户端(可选)。
*   Web 服务器充当代理以避免 CORS 问题(可选)。

## 2-浏览器网络工具🔥🔥

网络工具是有史以来最被低估的开发工具。在本教程中，我们将使用 firefox 的一些网络工具功能，即过滤器和复制为 cURL。
[![Firefox dev tools](img/d215e7256624da52bdd589a42d22447c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r5_CM9Ek--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://0x0.st/zexr.png) 
你要做的第一件事就是游览 fb.com/events 并打开网络工具。施展一些过滤魔法。脸书向/events 发出一个 post 请求，我们想要的是拦截这个请求并分析它的内容。

`method: POST regexp:events/`

然后点击请求行，让我们看看结果。如果什么都没有出现，向下滚动一点，直到浏览器发出另一个发布请求。

## 3-让我们来探索这个东西

下一步是探索请求体并从中学习。老实说，我不知道这些参数是什么意思，是做什么用的，但是好吧！

首先，让我们右键单击请求，并从复制菜单中选择复制 cURL。

好吧，让我们再做一些。下一步复制粘贴到你的终端(你有 curl 或一些 curl 兼容工具)和 Tadaaa！！有点长了，不是吗？？让我们把它分解成:

```
curl 'https://www.facebook.com/events/discover/query/' --data 'suggestion_token=%7B%22city%22%3A%22default_108085075885850%22%7D&timezone_id=1&__a=1' > events.json 
```

这是您立即获得活动所需的全部内容！最有用的参数是 *suggestion_token* ,它包含关于您希望事件发生的地点、主题和日期的首选项...

## 4- Hakuna Matata！让我们做点东西

现在我们需要使用我们的事件(例如在一个漂亮的网页上显示它们)。为此，我们需要一个浏览器 http 客户端库(或者获取 API 或者只是我们的老朋友 XHR)。但是这不会给我们想要的，因为该死的浏览器会阻止我们的跨来源请求(为了我们的 ofc🙄).
所以在这种情况下，我们需要设置一个代理(web 服务器)，它将为我们做抓取。然后，我们将通过 fetch 或 jquery 或 axios 或其他方式在客户端获取 json。
再次步骤:

*   因为我们懒得用我们最喜欢的语言重写 http 请求，所以我们要用这个网站把 cURL 语法翻译成 whatever 语法:p . [curl to whatever](https://curl.trillworks.com)

```
import requests
data = {  'suggestion_token': '{"city":"default_108085075885850"}',  'timezone_id': '1',  '__a': '1'}
response = requests.post('https://www.facebook.com/events/discover/query/', data=data) 
```

*   设置一个 web 服务器来提供必须提供的服务。同样，我们懒得设置一个合适的代理(使用 flask 或其他工具)或至少写一个像样的代理。因此，我们将在不到 1 或 2 分钟的时间内编写一个简单的脚本..Daaaamn，这太乱了:p

```
from http.server import HTTPServer, BaseHTTPRequestHandler
import requests

class SimpleHTTPRequestHandler(BaseHTTPRequestHandler):    
    def do_GET(self):        
        data = {
          'suggestion_token': '{"city":"default_108085075885850"}',
          'timezone_id': '1',
          '__a': '1'
        }
        response = requests.post('https://www.facebook.com/events/discover/query/', data=data)
        self.send_response(200)
        self.send_header("Access-Control-Allow-Origin", "*") # this is the key line of code !
        self.end_headers()
        self.wfile.write(response.text.encode())

PORT = 8080
httpd = HTTPServer(("", PORT), SimpleHTTPRequestHandler)
httpd.serve_forever() 
```

*   用 axios 或者 fetch 什么的把它放在你漂亮的网页里。

```
axios.get('localhost:8080')
  .then(function (response) {
    parseAndAddToMyBeautifulWebPage(response);    // beware the for(;;); in the beginning of the json data 
  }) 
```

就是这样！感谢您阅读:D，抱歉造成混乱。