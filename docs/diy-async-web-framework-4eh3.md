# DIY 异步 Web 框架🙌

> 原文：<https://dev.to/hzlmn/diy-async-web-framework-4eh3>

最初发表于[github.com/hzlmn/diy-async-web-framework](https://github.com/hzlmn/diy-async-web-framework)

如果你想知道像`aiohttp`和`sanic`这样的现代框架是如何工作的，那么这个指南就是为你准备的。

## 简介

在过去的几年中，异步编程在 Python 社区中变得非常流行。像`aiohttp`这样的库显示出令人难以置信的使用增长。它们处理大量并发连接，同时仍然保持良好的代码可读性和简单性。不久前，Django [承诺在下一个主要版本中加入异步支持。因此，正如你可能意识到的那样，异步 python 的未来非常光明。然而，对于大量来自标准模块化模型的开发人员来说，这些工具的工作机制可能会令人困惑。因此，在这个简短的指南中，我试图深入幕后，通过从头开始重新构建一个小小的`aiohttp`克隆来阐明这个过程。我们将从官方文档中基本示例开始，逐步添加我们都喜欢的所有必要功能。那我们开始吧。](https://docs.djangoproject.com/en/dev/releases/3.0/#asgi-support)

我假设你已经对 asyncio 有了基本的了解，可以遵循这个指南，但是如果你需要复习，这里有几篇文章可能会有帮助

*   [asyncio 简介](https://www.blog.pythonlibrary.org/2016/07/26/python-3-an-intro-to-asyncio)
*   [了解 Python 中的异步编程](https://dbader.org/blog/understanding-asynchronous-programming-in-python)

对于门诊患者，最终源代码可在 [`hzlmn/sketch`](https://github.com/hzlmn/sketch) 获得

### 目录📖

*   [Asyncio 低层 API、传输和协议](#asyncio-low-level-apis-transports-and-protocols)

*   [制定服务器协议](#making-server-protocol)

*   [请求/响应对象](#requestresponse-objects)

*   [应用程序和 UrlDispatcher](#application-and-urldispatcher)

*   [更进一步](#going-further)

    *   [路线参数](#route-params)
    *   [中间件](#middlewares)
    *   [应用生命周期挂钩](#app-lifecycle-hooks)
    *   [更好的例外](#better-exceptions)
    *   [正常关机](#graceful-shutdown)
*   [样本应用](#sample-application)

*   [结论](#conclusion)

## Asyncio 底层 API、传输和协议

Asyncio 经历了漫长的旅程才变成现在的样子。在那个时候，它是作为一种被称为“郁金香”的低级工具创建的，编写高级应用程序并不像今天这样令人愉快。

现在对于大多数用例来说,`asyncio`是相当高级的 API，但它也为库作者提供了一组低级的助手来管理事件循环，并实现网络/ipc 协议。

开箱即用仅支持`TCP`、`UDP`、`SSL`和子流程。库实现自己的更高层次(HTTP，FTP 等。)基于基本传输和可用的 API。

通过链`Transport`和`Protocols`完成所有通信。简单地说，`Transport`描述了我们如何交换数据，而`Protocol`负责具体选择哪些数据。

`Asyncio`有一个非常棒的官方文档，所以你可以在这里[阅读更多关于它的信息](https://docs.python.org/3.8/library/asyncio-protocol.html#asyncio-transport)

首先，让我们编写一个简单的`TCP`服务器来回应消息。

`server.py`

```
import asyncio

class Server(asyncio.Protocol):
    def connection_made(self, transport):
        self._transport = transport

    def data_received(self, data):
        message = data.decode()

        self._transport.write(data)

        self._transport.close()

loop = asyncio.get_event_loop()

coro = loop.create_server(Server, '127.0.0.1', 8080)
server = loop.run_until_complete(coro)

try:
    loop.run_forever()
except KeyboardInterrupt:
    pass

server.close()
loop.run_until_complete(server.wait_closed())
loop.close() 
```

```
$ curl http://127.0.0.1:8080
GET / HTTP/1.1
Host: 127.0.0.1:8080
User-Agent: curl/7.54.0
Accept: */* 
```

从上面的例子中可以看出，代码非常简单，但是您可能意识到，对于编写高级应用程序来说，它还不具备可伸缩性。

由于`HTTP`在`TCP`传输上工作，我们已经可以向我们的服务器发送`HTTP`请求，然而，我们接收的是原始格式的请求，正如你可能猜到的，使用它会很烦人。所以下一步我们需要添加更好的`HTTP`处理机制。

## 制定服务器协议

让我们添加请求解析，这样我们可以提取一些有用的信息，如标题、正文、路径，并使用它们而不是原始文本。解析是一个复杂的主题，它肯定超出了本指南的范围，这就是为什么我们将使用 MagicStack 的 [httptools](https://github.com/MagicStack/httptools) 进行解析，因为它快速、标准兼容并且非常灵活。

另一方面，`aiohttp`拥有自己手写的基于 Python 的解析器，并绑定到节点的 [`http-parser`](https://github.com/nodejs/http-parser/tree/77310eeb839c4251c07184a5db8885a572a08352) 。

让我们编写我们的解析类，它将被用作我们的主`Server`类的 mixin。

`http_parser.py`

```
class HttpParserMixin:
    def on_body(self, data):
        self._body = data

    def on_url(self, url):
        self._url = url

    def on_message_complete(self):
        print(f"Received request to {self._url.decode(self._encoding)}")

    def on_header(self, header, value):
        header = header.decode(self._encoding)
        self._headers[header] = value.decode(self._encoding) 
```

现在，当我们有了工作的`HttpParserMixin`，让我们稍微修改一下我们的`Server`并应用 mixin。

`server.py`

```
import asyncio

from httptools import HttpRequestParser

from .http_parser import HttpParserMixin

class Server(asyncio.Protocol, HttpParserMixin):
    def __init__(self, loop):
        self._loop = loop
        self._encoding = "utf-8"
        self._url = None
        self._headers = {}
        self._body = None
        self._transport = None
        self._request_parser = HttpRequestParser(self)

    def connection_made(self, transport):
        self._transport = transport

    def connection_lost(self, *args):
        self._transport = None

    def data_received(self, data):
        # Pass data to our parser
        self._request_parser.feed_data(data) 
```

到目前为止，我们的服务器可以理解传入的`HTTP`请求，并从中获取一些重要信息。现在让我们尝试添加简单的跑步者。

`server.py`

```
if __name__ == "__main__":
    loop = asyncio.get_event_loop()
    serv = Server(loop)
    server = loop.run_until_complete(loop.create_server(lambda: serv, port=8080))

    try:
        print("Started server on ::8080")
        loop.run_until_complete(server.serve_forever())
    except KeyboardInterrupt:
        server.close()
        loop.run_until_complete(server.wait_closed())
        loop.stop() 
```

```
> python server.py
Started server on ::8080 
```

```
> curl http://127.0.0.1:8080/hello 
```

## 请求/响应对象

目前，我们有可以解析`HTTP`调用的工作服务器，但是对于我们的应用程序，我们需要更好的抽象来工作。

让我们创建一个基类`Request`,它将所有传入的`HTTP`请求信息组合在一起。我们将使用`yarl`库来处理网址，确保你安装了 pip。

`request.py`

```
import json

from yarl import URL

class Request:
    _encoding = "utf_8"

    def __init__(self, method, url, headers, version=None, body=None, app=None):
        self._version = version
        self._method = method.decode(self._encoding)
        self._url = URL(url.decode(self._encoding))
        self._headers = headers
        self._body = body

    @property
    def method(self):
        return self._method

    @property
    def url(self):
        return self._url

    @property
    def headers(self):
        return self._headers

    def text(self):
        if self._body is not None:
            return self._body.decode(self._encoding)

    def json(self):
        text = self.text()
        if text is not None:
            return json.loads(text)

    def __repr__(self):
        return f"<Request at 0x{id(self)}>" 
```

下一步，我们还需要一个结构，它将帮助我们以程序员友好的方式描述传出的`HTTP`响应，并将其转换为原始的`HTTP`，可以由`asyncio.Transport`处理。

`response.py`

```
import http.server

web_responses = http.server.BaseHTTPRequestHandler.responses

class Response:
    _encoding = "utf-8"

    def __init__(
        self,
        body=None,
        status=200,
        content_type="text/plain",
        headers=None,
        version="1.1",
    ):
        self._version = version
        self._status = status
        self._body = body
        self._content_type = content_type
        if headers is None:
            headers = {}
        self._headers = headers

    @property
    def body(self):
        return self._body

    @property
    def status(self):
        return self._status

    @property
    def content_type(self):
        return self._content_type

    @property
    def headers(self):
        return self._headers

    def add_body(self, data):
        self._body = data

    def add_header(self, key, value):
        self._headers[key] = value

    def __str__(self):
        """We will use this in our handlers, it is actually generation of raw HTTP response,
        that will be passed to our TCP transport
        """
        status_msg, _ = web_responses.get(self._status)

        messages = [
            f"HTTP/{self._version}  {self._status}  {status_msg}",
            f"Content-Type: {self._content_type}",
            f"Content-Length: {len(self._body)}",
        ]

        if self.headers:
            for header, value in self.headers.items():
                messages.append(f"{header}: {value}")

        if self._body is not None:
            messages.append("\r\n" + self._body)

        return "\r\n".join(messages)

    def __repr__(self):
        return f"<Response at 0x{id(self)}>" 
```

如你所见，代码非常简单，我们封装了所有的数据，并提供了适当的 getters。此外，我们还有一些用于读取正文`text`和`json`的助手，稍后会用到。我们还需要更新我们的`Server`来从消息中实际构造`Request`对象。

它应该在处理整个请求时创建，所以我们需要将它添加到解析器 mixin 中的`on_message_complete`事件处理程序中。

`http_parser.py`

```
class HttpParserMixin:
    ...

    def on_message_complete(self):
        self._request = self._request_class(
            version=self._request_parser.get_http_version(),
            method=self._request_parser.get_method(),
            url=self._url,
            headers=self._headers,
            body=self._body,
        )

    ... 
```

服务器也需要一点修改来创建`Response`对象和
传递编码值给`asyncio.Transport`。

`server.py`

```
from .response import Response
...

class Server(asyncio.Protocol, HttpParserMixin):
    ...

    def __init__(self, loop):
        ...
        self._request = None
        self._request_class = Request

    ...

    def data_received(self, data):
        self._request_parser.feed_data(data)

        resp = Response(body=f"Received request on {self._request.url}")
        self._transport.write(str(resp).encode(self._encoding))

        self._transport.close() 
```

现在运行我们的`server.py`，我们将能够看到`Received request on /path`对 curl 调用`http:localhost:8080/path`的响应。

## 应用程序和 UrlDispatcher

在这个阶段，我们已经有了简单的工作服务器，可以处理 HTTP 请求和用于处理请求周期的请求/响应对象。然而，我们手工制作工具包仍然缺少一些重要的概念。首先，现在我们只有一个主请求处理器，在大型应用程序中，我们有很多不同的路由，所以我们当然需要注册多个路由处理器的机制。

因此，让我们试着构建最简单的可能的`UrlDispatcher`，仅仅是具有内部字典的对象，它作为一个键方法和路径元组存储，作为一个值存储实际的处理程序。我们还需要一个处理程序来处理用户试图到达无法识别的路线的情况。

`router.py`

```
from .response import Response

class UrlDispatcher:
    def __init__(self):
        self._routes = {}

    async def _not_found(self, request):
         return Response(f"Not found {request.url} on this server", status=404)

    def add_route(self, method, path, handler):
        self._routes[(method, path)] = handler

    def resolve(self, request):
        key = (request.method, request.url.path)
        if key not in self._routes:
            return self._not_found
        return self._routes[key] 
```

当然，我们错过了很多东西，如参数化路线，但我们会在以后添加它们。现在让我们保持简单。

接下来我们需要一个`Application`容器，它将实际上把所有应用相关的信息结合在一起，因为处理底层`Server`对我们来说会很烦人。

```
import asyncio

from .router import UrlDispatcher
from .server import Server
from .response import Response

class Application:
    def __init__(self, loop=None):
        if loop is None:
            loop = asyncio.get_event_loop()

        self._loop = loop
        self._router = UrlDispatcher()

    @property
    def loop(self):
        return self._loop

    @property
    def router(self):
        return self._router

    def _make_server(self):
        return Server(loop=self._loop, handler=self._handler, app=self)

    async def _handler(self, request, response_writer):
        """Process incoming request"""
        handler = self._router.resolve(request)
        resp = await handler(request)

        if not isinstance(resp, Response):
            raise RuntimeError(f"expect Response instance but got {type(resp)}")

        response_writer(resp) 
```

我们需要稍微修改一下我们的`Server`并添加`response_writer`方法，它将负责将数据传递给 transport。初始化器也应该被改变以增加`handler`和`app`属性，它们将被用来调用相应的处理程序。

`server.py`

```
 class Server(asyncio.Protocol, HttpParserMixin):
    ...

    def __init__(self, loop, handler, app):
        self._loop = loop
        self._url = None
        self._headers = {}
        self._body = None
        self._transport = None
        self._request_parser = HttpRequestParser(self)
        self._request = None
        self._request_class = Request
        self._request_handler = handler
        self._request_handler_task = None

    def response_writer(self, response):
        self._transport.write(str(response).encode(self._encoding))
        self._transport.close()

    ... 
```

`http_parser.py`

```
class HttpParserMixin:
    def on_body(self, data):
        self._body = data

    def on_url(self, url):
        self._url = url

    def on_message_complete(self):
        self._request = self._request_class(
            version=self._request_parser.get_http_version(),
            method=self._request_parser.get_method(),
            url=self._url,
            headers=self._headers,
            body=self._body,
        )

        self._request_handler_task = self._loop.create_task(
            self._request_handler(self._request, self.response_writer)
        )

    def on_header(self, header, value):
        header = header.decode(self._encoding)
        self._headers[header] = value.decode(self._encoding) 
```

最后，当我们准备好基本功能，可以注册新的路由和处理程序时，让我们添加简单的助手来实际运行我们的应用程序实例(类似于`aiohttp`中的`web.run_app`)。

`application.py`

```
def run_app(app, host="127.0.0.1", port=8080, loop=None):
    if loop is None:
        loop = asyncio.get_event_loop()

    serv = app._make_server()
    server = loop.run_until_complete(
        loop.create_server(lambda: serv, host=host, port=port)
    )

    try:
        print(f"Started server on {host}:{port}")
        loop.run_until_complete(server.serve_forever())
    except KeyboardInterrupt:
        server.close()
        loop.run_until_complete(server.wait_closed())
        loop.stop() 
```

现在，是时候用我们最新的工具包制作简单的应用程序了。

`app.py`

```
import asyncio

from .response import Response
from .application import Application, run_app

app = Application()

async def handler(request):
    return Response(f"Hello at {request.url}")

app.router.add_route("GET", "/", handler)

if __name__ == "__main__":
    run_app(app) 
```

如果您运行它，然后向`/`发出`GET`请求，您将能够看到`Hello at /`和`404`对所有其他路由的响应。万岁，我们做到了，但仍有很大的改进空间。

```
$ curl 127.0.0.1:8080/
Hello at /

$ curl 127.0.0.1:8080/invalid
Not found /invalid on this server 
```

## 更进一步

到目前为止，我们已经启动并运行了所有的基本功能，但是我们仍然需要改变我们“框架”中的某些东西。首先，正如我们前面讨论过的，我们的路由器缺少参数化路由，这是所有现代库的“必备”特性。接下来，我们需要添加对中间件的支持，这也是一个非常常见和强大的概念。我非常喜欢的`aiohttp`的一个优点是应用程序生命周期挂钩(例如`on_startup`、`on_shutdown`、`on_cleanup`)，所以我们当然也应该尝试实现它。

## 路线参数

目前，我们的`UrlDispatcher`非常精简，它使用注册的 url 路径作为字符串。首先，我们需要的是在我们的`resolve`方法中添加对像`/user/{username}`这样的模式的支持。我们还需要`_format_pattern`助手，负责从参数化字符串生成实际的正则表达式。你可能也注意到了，我们有另一个助手`_method_not_allowed`和方法来简化`GET`、`POST`等的定义。路线。

`router.py`

```
import re

from functools import partialmethod

from .response import Response

class UrlDispatcher:
    _param_regex = r"{(?P<param>\w+)}"

    def __init__(self):
        self._routes = {}

    async def _not_found(self, request):
        return Response(f"Could not find {request.url.raw_path}")

    async def _method_not_allowed(self, request):
        return Response(f"{request.method} not allowed for {request.url.raw_path}")

    def resolve(self, request):
        for (method, pattern), handler in self._routes.items():
            match = re.match(pattern, request.url.raw_path)

            if match is None:
                return None, self._not_found

            if method != request.method:
                return None, self._method_not_allowed

            return match.groupdict(), handler

    def _format_pattern(self, path):
        if not re.search(self._param_regex, path):
            return path

        regex = r""
        last_pos = 0

        for match in re.finditer(self._param_regex, path):
            regex += path[last_pos: match.start()]
            param = match.group("param")
            regex += r"(?P<%s>\w+)" % param
            last_pos = match.end()

        return regex

    def add_route(self, method, path, handler):
        pattern = self._format_pattern(path)
        self._routes[(method, pattern)] = handler

    add_get = partialmethod(add_route, "GET")

    add_post = partialmethod(add_route, "POST")

    add_put = partialmethod(add_route, "PUT")

    add_head = partialmethod(add_route, "HEAD")

    add_options = partialmethod(add_route, "OPTIONS") 
```

我们还需要修改我们的应用程序容器，现在`UrlDispatcher`的`resolve`方法返回`match_info`和`handler`。所以在`Application._handler`里面修改以下几行。

`application.py`

```
class Application:
    ...
    async def _handler(self, request, response_writer):
        """Process incoming request"""
        match_info, handler = self._router.resolve(request)

        request.match_info = match_info

        ... 
```

## 中间件

对于那些不熟悉这个概念的人来说，简单地说`middleware`只是一个协程，它可以修改传入的请求对象或改变处理程序的响应。它将在每次向服务器发出请求之前被触发。对于我们的需求来说，实现是微不足道的。首先，我们需要在我们的`Application`对象中添加注册的中间件列表，并稍微修改一下`Application._handler`来运行它们。每个中间件都应该与链中前一个的结果一起工作。

`application.py`

```
from functools import partial
...

class Application:
    def __init__(self, loop=None, middlewares=None):
        ...
        if middlewares is None:
            self._middlewares = []

    ...

    async def _handler(self, request, response_writer):
        """Process incoming request"""
        match_info, handler = self._router.resolve(request)

        request.match_info = match_info

        if self._middlewares:
            for md in self._middlewares:
                handler = partial(md, handler=handler)

        resp = await handler(request)

        ... 
```

现在让我们尝试将请求日志中间件添加到我们的简单应用程序中。

`app.py`

```
import asyncio

from .response import Response
from .application import Application, run_app

async def log_middleware(request, handler):
    print(f"Received request to {request.url.raw_path}")
    return await handler(request)

app = Application(middlewares=[log_middleware])

async def handler(request):
    return Response(f"Hello at {request.url}")

app.router.add_route("GET", "/", handler)

if __name__ == "__main__":
    run_app(app) 
```

如果我们尝试运行它，我们应该看到响应传入请求的`Received request to /`消息。

## App 生命周期挂钩

下一步，让我们添加对运行某些协程的支持，以响应像启动和停止服务器这样的事件。这是`aiohttp`非常简洁的特征。有许多信号，比如`on_startup`、`on_shutdown`、`on_response_prepared`等等，但是为了我们的需要，让我们尽可能简单，只实现`startup`、&、`shutdown`助手。

在`Application`中，我们需要为每个事件添加实际处理程序列表，并提供 getters。然后实际的`startup`和`shutdown`协程并添加相应的调用给`run_app`助手。

`application.py`

```
class Application:
    def __init__(self, loop=None, middlewares=None):
        ...
        self._on_startup = []
        self._on_shutdown = []

    ... 

    @property
    def on_startup(self):
        return self._on_startup

    @property
    def on_shutdown(self):
        return self._on_shutdown

    async def startup(self):
        coros = [func(self) for func in self._on_startup]
        await asyncio.gather(*coros, loop=self._loop)

    async def shutdown(self):
        coros = [func(self) for func in self._on_shutdown]
        await asyncio.gather(*coros, loop=self._loop)

    ...

def run_app(app, host="127.0.0.1", port=8080, loop=None):
    if loop is None:
        loop = asyncio.get_event_loop()

    serv = app._make_server()

    loop.run_until_complete(app.startup())

    server = loop.run_until_complete(
        loop.create_server(lambda: serv, host=host, port=port)
    )

    try:
        print(f"Started server on {host}:{port}")
        loop.run_until_complete(server.serve_forever())
    except KeyboardInterrupt:
        loop.run_until_complete(app.shutdown())
        server.close()
        loop.run_until_complete(server.wait_closed())
        loop.stop() 
```

## 更好的例外

在这一步，我们添加了大部分核心特性，但是，我们仍然缺少异常处理。关于`aiohttp`的一个很棒的特性是它允许你像处理本地 python 异常一样处理 web 异常。
它通过实现`Exception`和`Response`类来完成，这也是我们想要的真正灵活的机制。

所以，首先让我们创建我们的基类`HTTPException`和一些基于它的帮助器，我们可能需要像`HTTPNotFound`用于未识别的路径`HTTPBadRequest`用于用户端问题和`HTTPFound`用于重定向。

```
from .response import Response

class HTTPException(Response, Exception):
    status_code = None

    def __init__(self, reason=None, content_type=None):
        self._reason = reason
        self._content_type = content_type

        Response.__init__(
            self,
            body=self._reason,
            status=self.status_code,
            content_type=self._content_type or "text/plain",
        )

        Exception.__init__(self, self._reason)

class HTTPNotFound(HTTPException):
    status_code = 404

class HTTPBadRequest(HTTPException):
    status_code = 400

class HTTPFound(HTTPException):
    status_code = 302

    def __init__(self, location, reason=None, content_type=None):
        super().__init__(reason=reason, content_type=content_type)
        self.add_header("Location", location) 
```

然后我们需要稍微修改一下我们的`Application._handler`来实际捕捉 web 异常。

`application.py`

```
class Application:
    ...
    async def _handler(self, request, response_writer):
        """Process incoming request"""
        try:
            match_info, handler = self._router.resolve(request)

            request.match_info = match_info

            if self._middlewares:
                for md in self._middlewares:
                    handler = partial(md, handler=handler)

            resp = await handler(request)
        except HTTPException as exc:
            resp = exc

        ... 
```

现在我们也可以从我们的`UrlDispatcher`中删除`_not_found` & `_method_not_allowed`助手，取而代之的是抛出适当的异常。

`router.py`

```
class UrlDispatcher:
    ...
    def resolve(self, request):
        for (method, pattern), handler in self._routes.items():
            match = re.match(pattern, request.url.raw_path)

            if match is None:
                raise HTTPNotFound(reason=f"Could not find {request.url.raw_path}")

            if method != request.method:
                raise HTTPBadRequest(reason=f"{request.method} not allowed for {request.url.raw_path}")

            return match.groupdict(), handler

        ... 
```

另一个很好的补充是内部服务器错误的标准格式响应，因为我们不想在一些不一致的情况下破坏实际的应用程序。让我们添加一个简单的 html 模板和一个用于格式化异常的小助手。

`helpers.py`

```
import traceback

from .response import Response

server_exception_templ = """
<div>
    <h1>500 Internal server error</h1>
    <span>Server got itself in trouble : <b>{exc}</b><span>
    <p>{traceback}</p>
</div>
"""

def format_exception(exc):
    resp = Response(status=500, content_type="text/html")
    trace = traceback.format_exc().replace("\n", "</br>")
    msg = server_exception_templ.format(exc=str(exc), traceback=trace)
    resp.add_body(msg)
    return resp 
```

简单得很，现在只需捕获我们的`Application._handler`中的所有`Exception`，并使用我们的助手生成实际的 html 响应。

`application.py`

```
class Application:
    ...
    async def _handler(self, request, response_writer):
        """Process incoming request"""
        try:
            match_info, handler = self._router.resolve(request)

            request.match_info = match_info

            if self._middlewares:
                for md in self._middlewares:
                    handler = partial(md, handler=handler)

            resp = await handler(request)
        except HTTPException as exc:
            resp = exc
        except Exception as exc:
            resp = format_exception(exc)
        ... 
```

## 优雅关机

最后，我们需要为关闭应用程序的正确过程添加信号处理。所以，我们把`run_app`改成下面几行。

`application.py`

```
...

def run_app(app, host="127.0.0.1", port=8080, loop=None):
    if loop is None:
        loop = asyncio.get_event_loop()

    serv = app._make_server()

    loop.run_until_complete(app.startup())

    server = loop.run_until_complete(
        loop.create_server(lambda: serv, host=host, port=port)
    )

    loop.add_signal_handler(
        signal.SIGTERM, lambda: asyncio.ensure_future(app.shutdown())
    )

    ... 
```

## 样本应用

现在，当我们准备好工具包后，让我们试着完成我们之前添加的示例应用程序生命周期挂钩和异常。

`app.py`

```
from .application import Application, run_app

async def on_startup(app):
    # you may query here actual db, but for an example let's just use simple set.
    app.db = {"john_doe",}

async def log_middleware(request, handler):
    print(f"Received request to {request.url.raw_path}")
    return await handler(request)

async def handler(request):
    username = request.match_info["username"]
    if username not in request.app.db:
        raise HTTPNotFound(reason=f"No such user with as {username} :(")

    return Response(f"Welcome, {username}!")

app = Application(middlewares=[log_middleware])

app.on_startup.append(on_startup)

app.router.add_get("/{username}", handler)

if __name__ == "__main__":
    run_app(app) 
```

如果我们做了所有正确的事情，你会看到每个请求的日志消息，注册用户的欢迎消息，以及未注册用户和未识别路径的`HTTPNotFound`。

## 结论

总结一下，在大约 500 行代码中，我们手工制作了一个非常简单但功能强大的微框架，灵感来自`aiohttp` & `sanic`。当然，它不是生产就绪软件，因为它仍然缺少许多有用的&重要功能，如更健壮的服务器、更好的 HTTP 支持以与规范完全相关、web 套接字等等。然而，我相信通过这个过程，我们对这些工具是如何构建的有了更好的理解。正如著名物理学家理查德·费曼所说，“我不能创造的东西，我不理解”。所以我希望你喜欢这个指南，再见！👋