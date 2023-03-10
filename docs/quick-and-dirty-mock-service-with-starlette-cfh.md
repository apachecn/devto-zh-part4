# 快速和肮脏的模拟服务

> 原文：<https://dev.to/mblayman/quick-and-dirty-mock-service-with-starlette-cfh>

我在工作中遇到了挑战。该团队需要在测试环境中模拟第三方服务。这项服务速度很慢，而且配置起来很痛苦。如果我们能模拟出来，那么团队就能避免这些问题。

模拟服务的挑战在于，部分流程需要调用一个 webhook，该 web hook 将回调我公司的系统，以指示所有工作都已完成。此外，webhook 调用必须经过延迟，因为我们模拟的服务需要很长时间(例如，超过 60 秒)。

> **问题:**运行一个带有延迟 webhook 回调的模拟第三方服务。

该解决方案需要:

*   响应 POST 请求并返回一个`200 OK`状态。
*   将一个 webhook 回调到系统，该系统将包含额外的标识符来将 webhook 调用连接到原始请求。
*   尽可能简单，因为这不是核心产品。

## 解决方案选项

Python 生态系统充满了解决方案第一部分的强大选项。Django、Flask、Pyramid、Bottle 和任何你能想到的其他 web 框架都可以轻松处理这个问题。

解决方案的第二部分更难。如果我不小心，那么简单就过时了，我已经破坏了第三个目标。

### 后台任务工具

虽然我很喜欢姜戈，但我觉得这不太合适。在我的思考过程中，稍后调用 webhook 需要某种后台任务。如果你知道姜戈，那么你可能会立即想到[芹菜](http://www.celeryproject.org/)，这也是我的第一个想法。问题是芹菜需要一个像 [RabbitMQ](https://www.rabbitmq.com/) 这样的消息代理。*这样一来，简约预算就完全泡汤了。*

芹菜的一个替代品是 [RQ](https://python-rq.org/) ，但是它有类似的问题，并且需要运行 [Redis](https://redis.io/) 以及一个单独的工人进程。

> 我们可以不使用单独的后台任务工具来解决这个问题吗？

### Sans 后台任务工具

我知道简单是一个重要的目标，我真的想在没有后台任务工具的情况下完成这个任务。

我考虑了两种方法:

1.  使用线程。
2.  使用协作并发(阅读:异步编程)

我不喜欢 Python 中的线程编程。其实我不喜欢线程编程。一个线程化的解决方案完全可以通过在一个单独的线程中执行 webhook 调用来工作，但是我对其他选项感兴趣。

我能用异步编程解决这个问题吗？在最新版本的 Python 中，异步编程要容易得多。这种语言现在有像`async`和`await`这样的内置关键字，使得这种风格的编程更加友好。我评估了一些异步 web 框架，我回忆起我在 [Starlette](https://www.starlette.io/) 文档中看到的一个特性:[后台任务](https://www.starlette.io/background/)

Starlette 是由 [Django REST Framework (DRF)](https://www.django-rest-framework.org/) 、 [Tom Christie](https://twitter.com/_tomchristie) 的作者开发的 web 框架。DRF 是这样一个坚实的项目。共享同一个创作者增强了我的信心，Starlette 将是一个设计良好的软件。

我决定跟进这个项目，看看它是否可行。一个小时后，我震惊地看到我有了一个最终的，*工作*，解决方案。

本文的其余部分将探讨我是如何使用 Starlette 解决这个问题的，Starlette 的通用版本跳过了第三方服务的无聊细节。

## 小明星

由于 Starlette 是一个异步框架，支持 Starlette 应用程序所需的 Python 工具不同于标准的 [WSGI](https://www.python.org/dev/peps/pep-3333/) 应用程序。

不需要像 [Gunicorn](https://gunicorn.org/) 或 [uWSGI](https://uwsgi-docs.readthedocs.io/en/latest/) 那样的*同步* web 服务器，而是需要一个*异步* web 服务器。我选择了[uvicon](https://www.uvicorn.org/)，一个类似于 Gunicorn 的 web 服务器，它使用 [uvloop](https://uvloop.readthedocs.io/) 来处理异步事件循环。

为了进行 webhook 回调，模拟器需要发出 HTTP 请求。要达到的自然包是[请求](https://requests.readthedocs.io/en/master/)，但是`requests`是一个同步包，它会阻塞事件循环。我需要一个异步 HTTP 请求库。对于这个需求，我选择了 [HTTPX](https://www.encode.io/httpx/) 。HTTPX 是一个非常新的库，它与`requests` **共享相同的 API，而**支持异步编程。

如果您想跟进，首先，安装所有必要的依赖项。你至少需要 Python 3.6。

```
$ mkdir mock-service
$ cd mock-service
$ python3 -m venv venv
$ . venv/bin/activate
(venv) $ pip install starlette uvicorn httpx 
```

对于未来的读者来说，这里是在撰写本文时,`pip install`命令安装到我的本地虚拟环境中的版本。

```
$ pip freeze
certifi==2019.6.16
chardet==3.0.4
Click==7.0
h11==0.8.1
h2==3.1.1
hpack==3.0.0
hstspreload==2019.8.20
httptools==0.0.13
httpx==0.7.1
hyperframe==5.2.0
idna==2.8
rfc3986==1.3.2
starlette==0.12.8
uvicorn==0.8.6
uvloop==0.12.2
websockets==7.0 
```

让我们首先列出完整的示例。我将分解应用程序的每个部分，但有时它有助于首先获得完整的图片。要亲自尝试一下，您可以使用示例脚本。

```
import asyncio
import os
import uuid

import httpx
from starlette.applications import Starlette
from starlette.background import BackgroundTask
from starlette.responses import JSONResponse
import uvicorn

app = Starlette()
client = httpx.AsyncClient()
CALLBACK_URL = os.environ["CALLBACK_URL"]

@app.route("/api/endpoint", methods=["POST"])
async def fake_endpoint(request):
    identifier = str(uuid.uuid4())
    payload = {
        "identifier": identifier,
        "some_parameter": request.query_params.get("some_parameter"),
    }
    task = BackgroundTask(trigger_webhook, payload)
    return JSONResponse(
        {"identifier": identifier, "success": True}, background=task)

async def trigger_webhook(payload):
    await asyncio.sleep(5)
    params = {
        "success": True,
        "identifier": payload["identifier"],
        "some_parameter": payload["some_parameter"],
    }
    await client.get(CALLBACK_URL, params=params)

if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=8000) 
```

### 运行代码

我们可以端到端地运行代码，以模拟如何将其插入到真实环境中。

首先，我们需要一个 webhook 接收器。Python 内置的 HTTP 服务器非常适合这项任务。在单独的终端中，运行:

```
$ python3 -m http.server 5000
Serving HTTP on 0.0.0.0 port 5000 (http://0.0.0.0:5000/) ... 
```

接下来，启动模拟服务。

```
(venv) $ CALLBACK_URL=http://0.0.0.0:5000 python3 mock_service.py
INFO: Started server process [47148]
INFO: Waiting for application startup.
INFO: Uvicorn running on http://0.0.0.0:8000 (Press CTRL+C to quit) 
```

请注意，我已经将`CALLBACK_URL`作为环境变量传入，并带有来自我们的 webhook 接收器的 URL。模拟服务将使用这些信息来知道将 webhook 请求发送到哪里。

核心部分就绪后，我们需要触发模拟服务。我喜欢 HTTPie T1，它比 T2 curl T3 更友好。

```
(venv) $ pip install httpie 
```

现在我们可以让一切都动起来了！让我们给模拟服务发一个帖子。

```
(venv) $ http POST :8000/api/endpoint some_parameter==some_value
HTTP/1.1 200 OK
content-length: 68
content-type: application/json
date: Sat, 24 Aug 2019 15:54:07 GMT
server: uvicorn

{
    "identifier": "4a3a0ce2-ae0c-41d3-ba58-89a5a9579692",
    "success": true
} 
```

在模拟服务中，您将看到一个类似于
的日志

```
INFO: ('127.0.0.1', 61647) - \
    "POST /api/endpoint?some_parameter=some_value HTTP/1.1" 200 
```

五秒钟后，假的 webhook 接收器应该显示:

```
127.0.0.1 - - [24/Aug/2019 11:54:12] \
    "GET /?success=true&identifier= \ 4a3a0ce2-ae0c-41d3-ba58-89a5a9579692&some_parameter=some_value \ HTTP/1.1" 200 - 
```

我已经重新编排了这些行的格式，以便它们更适合这篇文章。

**牛逼！模拟服务正是我们想要的。POST 请求得到了成功的响应，经过一段时间后，webhook 得到了一些相同的信息。**

既然我们已经看到了工作，让我们来分解代码。

### 模拟服务解释

我们可以把这段代码分成几个独立的部分来看。

```
import asyncio
import os
import uuid

import httpx
from starlette.applications import Starlette
from starlette.background import BackgroundTask
from starlette.responses import JSONResponse
import uvicorn 
```

进口部分不是很令人兴奋。我已经将标准库导入和第三方导入区分开来。如果我们在单独的文件中有更多的代码，那么我将有第三部分用于本地导入，但是我们不需要这样做，因为整个模拟器文件只有 39 行。

```
app = Starlette()
client = httpx.AsyncClient()
CALLBACK_URL = os.environ["CALLBACK_URL"] 
```

本节创建我们将要使用的模块级全局变量。有时，全球化是工作的最佳工具，即使它们经常被人诟病。`CALLBACK_URL`是仿真器的唯一设置。注意，我用索引语法从`os.environ`中获取值，而不是用`os.environ.get('CALLBACK_URL')`语法。获取的索引样式将确保我们有值，因为它是正确操作模拟器所必需的。

```
@app.route("/api/endpoint", methods=["POST"])
async def fake_endpoint(request):
    identifier = str(uuid.uuid4())
    payload = {
        "identifier": identifier,
        "some_parameter": request.query_params.get("some_parameter"),
    }
    task = BackgroundTask(trigger_webhook, payload)
    return JSONResponse(
        {"identifier": identifier, "success": True}, background=task) 
```

这个异步函数定义了核心路由。这种风格应该是 Flask 用户非常熟悉的。该函数的主体做一些事情:

1.  生成一个标识符，调用者可以用它将这个调用与后面的 webhook 关联起来。
2.  从请求的查询字符串`some_parameter`中提取一个值，并为后台任务存储它。
3.  将数据捆绑到一个有效载荷中，该有效载荷将对执行`trigger_webhook`功能的`BackgroundTask`可用。
4.  用标识符进行响应，并设置后台任务运行。

这个模拟器的一个奇怪的方面是它处理 POST 请求，但是没有处理 POST 数据。真正的第三方服务包括帖子中的 PDF 文件，但我想忽略这一点，因为它没有说明任何额外的价值。

```
async def trigger_webhook(payload):
    await asyncio.sleep(5)
    params = {
        "success": True,
        "identifier": payload["identifier"],
        "some_parameter": payload["some_parameter"],
    }
    await client.get(CALLBACK_URL, params=params) 
```

webhook 触发器休眠 5 秒钟。用`asyncio.sleep`代替`time.sleep`很重要。来自`time`的睡眠功能是一个同步命令，将阻止事件循环。通过用`await`调用`asyncio.sleep`，该函数将执行返回到事件循环，直到 5 秒过去。

一旦延迟结束，HTTPX `client`调用回调 URL 来返回标识符和参数。这向调用方表明模拟器已经完成。

```
if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=8000) 
```

最后一段代码将应用程序连接到 uvicon，这样当服务被调用时 uvicon 就会启动:

```
(venv) $ python3 mock_service.py 
```

### 小明星前途无量

Starlette 的 API 使得这个模拟器的生产速度达到了我的预期。这个框架还是新的，但是我认为它在 Python 生态系统中有着非常光明的未来。在 [TechEmpower 基准测试](https://www.techempower.com/benchmarks/#section=data-r18&hw=ph&test=query&l=zijzen-f)中，它已经拥有了所有 Python 框架中最好的性能。

我希望你喜欢看 Python 中的一些异步编程。如果您有任何问题，请在 Twitter 上分享，并通过 [@mblayman](https://twitter.com/mblayman) 联系我。

感谢阅读！

这篇文章最初发表在 mattlayman.com 的上。

* * *

1.  我一直在研究 Rust，所以也许这种语言可以改变我对线程编程的看法，但结论仍然没有出来。 [↩](#fnref1)