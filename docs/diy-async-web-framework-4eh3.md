# DIY å¼æ­¥ Web æ¡æ¶ð

> åæï¼<https://dev.to/hzlmn/diy-async-web-framework-4eh3>

æååè¡¨äº[github.com/hzlmn/diy-async-web-framework](https://github.com/hzlmn/diy-async-web-framework)

å¦æä½ æ³ç¥éå`aiohttp`å`sanic`è¿æ ·çç°ä»£æ¡æ¶æ¯å¦ä½å·¥ä½çï¼é£ä¹è¿ä¸ªæåå°±æ¯ä¸ºä½ åå¤çã

## ç®ä»

å¨è¿å»çå å¹´ä¸­ï¼å¼æ­¥ç¼ç¨å¨ Python ç¤¾åºä¸­åå¾éå¸¸æµè¡ãå`aiohttp`è¿æ ·çåºæ¾ç¤ºåºä»¤äººé¾ä»¥ç½®ä¿¡çä½¿ç¨å¢é¿ãå®ä»¬å¤çå¤§éå¹¶åè¿æ¥ï¼åæ¶ä»ç¶ä¿æè¯å¥½çä»£ç å¯è¯»æ§åç®åæ§ãä¸ä¹åï¼Django [æ¿è¯ºå¨ä¸ä¸ä¸ªä¸»è¦çæ¬ä¸­å å¥å¼æ­¥æ¯æãå æ­¤ï¼æ­£å¦ä½ å¯è½æè¯å°çé£æ ·ï¼å¼æ­¥ python çæªæ¥éå¸¸åæãç¶èï¼å¯¹äºå¤§éæ¥èªæ åæ¨¡ååæ¨¡åçå¼åäººåæ¥è¯´ï¼è¿äºå·¥å·çå·¥ä½æºå¶å¯è½ä¼ä»¤äººå°æãå æ­¤ï¼å¨è¿ä¸ªç®ç­çæåä¸­ï¼æè¯å¾æ·±å¥å¹åï¼éè¿ä»å¤´å¼å§éæ°æå»ºä¸ä¸ªå°å°ç`aiohttp`åéæ¥éæè¿ä¸ªè¿ç¨ãæä»¬å°ä»å®æ¹ææ¡£ä¸­åºæ¬ç¤ºä¾å¼å§ï¼éæ­¥æ·»å æä»¬é½åæ¬¢çææå¿è¦åè½ãé£æä»¬å¼å§å§ã](https://docs.djangoproject.com/en/dev/releases/3.0/#asgi-support)

æåè®¾ä½ å·²ç»å¯¹ asyncio æäºåºæ¬çäºè§£ï¼å¯ä»¥éµå¾ªè¿ä¸ªæåï¼ä½æ¯å¦æä½ éè¦å¤ä¹ ï¼è¿éæå ç¯æç« å¯è½ä¼æå¸®å©

*   [asyncio ç®ä»](https://www.blog.pythonlibrary.org/2016/07/26/python-3-an-intro-to-asyncio)
*   [äºè§£ Python ä¸­çå¼æ­¥ç¼ç¨](https://dbader.org/blog/understanding-asynchronous-programming-in-python)

å¯¹äºé¨è¯æ£èï¼æç»æºä»£ç å¯å¨ [`hzlmn/sketch`](https://github.com/hzlmn/sketch) è·å¾

### ç®å½ð

*   [Asyncio ä½å± APIãä¼ è¾ååè®®](#asyncio-low-level-apis-transports-and-protocols)

*   [å¶å®æå¡å¨åè®®](#making-server-protocol)

*   [è¯·æ±/ååºå¯¹è±¡](#requestresponse-objects)

*   [åºç¨ç¨åºå UrlDispatcher](#application-and-urldispatcher)

*   [æ´è¿ä¸æ­¥](#going-further)

    *   [è·¯çº¿åæ°](#route-params)
    *   [ä¸­é´ä»¶](#middlewares)
    *   [åºç¨çå½å¨ææé©](#app-lifecycle-hooks)
    *   [æ´å¥½çä¾å¤](#better-exceptions)
    *   [æ­£å¸¸å³æº](#graceful-shutdown)
*   [æ ·æ¬åºç¨](#sample-application)

*   [ç»è®º](#conclusion)

## Asyncio åºå± APIãä¼ è¾ååè®®

Asyncio ç»åäºæ¼«é¿çæç¨æåæç°å¨çæ ·å­ãå¨é£ä¸ªæ¶åï¼å®æ¯ä½ä¸ºä¸ç§è¢«ç§°ä¸ºâééé¦âçä½çº§å·¥å·åå»ºçï¼ç¼åé«çº§åºç¨ç¨åºå¹¶ä¸åä»å¤©è¿æ ·ä»¤äººæå¿«ã

ç°å¨å¯¹äºå¤§å¤æ°ç¨ä¾æ¥è¯´,`asyncio`æ¯ç¸å½é«çº§ç APIï¼ä½å®ä¹ä¸ºåºä½èæä¾äºä¸ç»ä½çº§çå©ææ¥ç®¡çäºä»¶å¾ªç¯ï¼å¹¶å®ç°ç½ç»/ipc åè®®ã

å¼ç®±å³ç¨ä»æ¯æ`TCP`ã`UDP`ã`SSL`åå­æµç¨ãåºå®ç°èªå·±çæ´é«å±æ¬¡(HTTPï¼FTP ç­ã)åºäºåºæ¬ä¼ è¾åå¯ç¨ç APIã

éè¿é¾`Transport`å`Protocols`å®æææéä¿¡ãç®åå°è¯´ï¼`Transport`æè¿°äºæä»¬å¦ä½äº¤æ¢æ°æ®ï¼è`Protocol`è´è´£å·ä½éæ©åªäºæ°æ®ã

`Asyncio`æä¸ä¸ªéå¸¸æ£çå®æ¹ææ¡£ï¼æä»¥ä½ å¯ä»¥å¨è¿é[éè¯»æ´å¤å³äºå®çä¿¡æ¯](https://docs.python.org/3.8/library/asyncio-protocol.html#asyncio-transport)

é¦åï¼è®©æä»¬ç¼åä¸ä¸ªç®åç`TCP`æå¡å¨æ¥ååºæ¶æ¯ã

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

ä»ä¸é¢çä¾å­ä¸­å¯ä»¥çåºï¼ä»£ç éå¸¸ç®åï¼ä½æ¯æ¨å¯è½æè¯å°ï¼å¯¹äºç¼åé«çº§åºç¨ç¨åºæ¥è¯´ï¼å®è¿ä¸å·å¤å¯ä¼¸ç¼©æ§ã

ç±äº`HTTP`å¨`TCP`ä¼ è¾ä¸å·¥ä½ï¼æä»¬å·²ç»å¯ä»¥åæä»¬çæå¡å¨åé`HTTP`è¯·æ±ï¼ç¶èï¼æä»¬æ¥æ¶çæ¯åå§æ ¼å¼çè¯·æ±ï¼æ­£å¦ä½ å¯è½çå°çï¼ä½¿ç¨å®ä¼å¾ç¦äººãæä»¥ä¸ä¸æ­¥æä»¬éè¦æ·»å æ´å¥½ç`HTTP`å¤çæºå¶ã

## å¶å®æå¡å¨åè®®

è®©æä»¬æ·»å è¯·æ±è§£æï¼è¿æ ·æä»¬å¯ä»¥æåä¸äºæç¨çä¿¡æ¯ï¼å¦æ é¢ãæ­£æãè·¯å¾ï¼å¹¶ä½¿ç¨å®ä»¬èä¸æ¯åå§ææ¬ãè§£ææ¯ä¸ä¸ªå¤æçä¸»é¢ï¼å®è¯å®è¶åºäºæ¬æåçèå´ï¼è¿å°±æ¯ä¸ºä»ä¹æä»¬å°ä½¿ç¨ MagicStack ç [httptools](https://github.com/MagicStack/httptools) è¿è¡è§£æï¼å ä¸ºå®å¿«éãæ åå¼å®¹å¹¶ä¸éå¸¸çµæ´»ã

å¦ä¸æ¹é¢ï¼`aiohttp`æ¥æèªå·±æåçåºäº Python çè§£æå¨ï¼å¹¶ç»å®å°èç¹ç [`http-parser`](https://github.com/nodejs/http-parser/tree/77310eeb839c4251c07184a5db8885a572a08352) ã

è®©æä»¬ç¼åæä»¬çè§£æç±»ï¼å®å°è¢«ç¨ä½æä»¬çä¸»`Server`ç±»ç mixinã

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

ç°å¨ï¼å½æä»¬æäºå·¥ä½ç`HttpParserMixin`ï¼è®©æä»¬ç¨å¾®ä¿®æ¹ä¸ä¸æä»¬ç`Server`å¹¶åºç¨ mixinã

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

å°ç®åä¸ºæ­¢ï¼æä»¬çæå¡å¨å¯ä»¥çè§£ä¼ å¥ç`HTTP`è¯·æ±ï¼å¹¶ä»ä¸­è·åä¸äºéè¦ä¿¡æ¯ãç°å¨è®©æä»¬å°è¯æ·»å ç®åçè·æ­¥èã

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

## è¯·æ±/ååºå¯¹è±¡

ç®åï¼æä»¬æå¯ä»¥è§£æ`HTTP`è°ç¨çå·¥ä½æå¡å¨ï¼ä½æ¯å¯¹äºæä»¬çåºç¨ç¨åºï¼æä»¬éè¦æ´å¥½çæ½è±¡æ¥å·¥ä½ã

è®©æä»¬åå»ºä¸ä¸ªåºç±»`Request`,å®å°ææä¼ å¥ç`HTTP`è¯·æ±ä¿¡æ¯ç»åå¨ä¸èµ·ãæä»¬å°ä½¿ç¨`yarl`åºæ¥å¤çç½åï¼ç¡®ä¿ä½ å®è£äº pipã

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

ä¸ä¸æ­¥ï¼æä»¬è¿éè¦ä¸ä¸ªç»æï¼å®å°å¸®å©æä»¬ä»¥ç¨åºååå¥½çæ¹å¼æè¿°ä¼ åºç`HTTP`ååºï¼å¹¶å°å¶è½¬æ¢ä¸ºåå§ç`HTTP`ï¼å¯ä»¥ç±`asyncio.Transport`å¤çã

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

å¦ä½ æè§ï¼ä»£ç éå¸¸ç®åï¼æä»¬å°è£äºææçæ°æ®ï¼å¹¶æä¾äºéå½ç gettersãæ­¤å¤ï¼æä»¬è¿æä¸äºç¨äºè¯»åæ­£æ`text`å`json`çå©æï¼ç¨åä¼ç¨å°ãæä»¬è¿éè¦æ´æ°æä»¬ç`Server`æ¥ä»æ¶æ¯ä¸­å®éæé `Request`å¯¹è±¡ã

å®åºè¯¥å¨å¤çæ´ä¸ªè¯·æ±æ¶åå»ºï¼æä»¥æä»¬éè¦å°å®æ·»å å°è§£æå¨ mixin ä¸­ç`on_message_complete`äºä»¶å¤çç¨åºä¸­ã

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

æå¡å¨ä¹éè¦ä¸ç¹ä¿®æ¹æ¥åå»º`Response`å¯¹è±¡å
ä¼ éç¼ç å¼ç»`asyncio.Transport`ã

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

ç°å¨è¿è¡æä»¬ç`server.py`ï¼æä»¬å°è½å¤çå°`Received request on /path`å¯¹ curl è°ç¨`http:localhost:8080/path`çååºã

## åºç¨ç¨åºå UrlDispatcher

å¨è¿ä¸ªé¶æ®µï¼æä»¬å·²ç»æäºç®åçå·¥ä½æå¡å¨ï¼å¯ä»¥å¤ç HTTP è¯·æ±åç¨äºå¤çè¯·æ±å¨æçè¯·æ±/ååºå¯¹è±¡ãç¶èï¼æä»¬æå·¥å¶ä½å·¥å·åä»ç¶ç¼ºå°ä¸äºéè¦çæ¦å¿µãé¦åï¼ç°å¨æä»¬åªæä¸ä¸ªä¸»è¯·æ±å¤çå¨ï¼å¨å¤§ååºç¨ç¨åºä¸­ï¼æä»¬æå¾å¤ä¸åçè·¯ç±ï¼æä»¥æä»¬å½ç¶éè¦æ³¨åå¤ä¸ªè·¯ç±å¤çå¨çæºå¶ã

å æ­¤ï¼è®©æä»¬è¯çæå»ºæç®åçå¯è½ç`UrlDispatcher`ï¼ä»ä»æ¯å·æåé¨å­å¸çå¯¹è±¡ï¼å®ä½ä¸ºä¸ä¸ªé®æ¹æ³åè·¯å¾åç»å­å¨ï¼ä½ä¸ºä¸ä¸ªå¼å­å¨å®éçå¤çç¨åºãæä»¬è¿éè¦ä¸ä¸ªå¤çç¨åºæ¥å¤çç¨æ·è¯å¾å°è¾¾æ æ³è¯å«çè·¯çº¿çæåµã

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

å½ç¶ï¼æä»¬éè¿äºå¾å¤ä¸è¥¿ï¼å¦åæ°åè·¯çº¿ï¼ä½æä»¬ä¼å¨ä»¥åæ·»å å®ä»¬ãç°å¨è®©æä»¬ä¿æç®åã

æ¥ä¸æ¥æä»¬éè¦ä¸ä¸ª`Application`å®¹å¨ï¼å®å°å®éä¸æææåºç¨ç¸å³çä¿¡æ¯ç»åå¨ä¸èµ·ï¼å ä¸ºå¤çåºå±`Server`å¯¹æä»¬æ¥è¯´ä¼å¾ç¦äººã

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

æä»¬éè¦ç¨å¾®ä¿®æ¹ä¸ä¸æä»¬ç`Server`å¹¶æ·»å `response_writer`æ¹æ³ï¼å®å°è´è´£å°æ°æ®ä¼ éç» transportãåå§åå¨ä¹åºè¯¥è¢«æ¹åä»¥å¢å `handler`å`app`å±æ§ï¼å®ä»¬å°è¢«ç¨æ¥è°ç¨ç¸åºçå¤çç¨åºã

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

æåï¼å½æä»¬åå¤å¥½åºæ¬åè½ï¼å¯ä»¥æ³¨åæ°çè·¯ç±åå¤çç¨åºæ¶ï¼è®©æä»¬æ·»å ç®åçå©ææ¥å®éè¿è¡æä»¬çåºç¨ç¨åºå®ä¾(ç±»ä¼¼äº`aiohttp`ä¸­ç`web.run_app`)ã

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

ç°å¨ï¼æ¯æ¶åç¨æä»¬ææ°çå·¥å·åå¶ä½ç®åçåºç¨ç¨åºäºã

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

å¦ææ¨è¿è¡å®ï¼ç¶åå`/`ååº`GET`è¯·æ±ï¼æ¨å°è½å¤çå°`Hello at /`å`404`å¯¹ææå¶ä»è·¯ç±çååºãä¸å²ï¼æä»¬åå°äºï¼ä½ä»æå¾å¤§çæ¹è¿ç©ºé´ã

```
$ curl 127.0.0.1:8080/
Hello at /

$ curl 127.0.0.1:8080/invalid
Not found /invalid on this server 
```

## æ´è¿ä¸æ­¥

å°ç®åä¸ºæ­¢ï¼æä»¬å·²ç»å¯å¨å¹¶è¿è¡äºææçåºæ¬åè½ï¼ä½æ¯æä»¬ä»ç¶éè¦æ¹åæä»¬âæ¡æ¶âä¸­çæäºä¸è¥¿ãé¦åï¼æ­£å¦æä»¬åé¢è®¨è®ºè¿çï¼æä»¬çè·¯ç±å¨ç¼ºå°åæ°åè·¯ç±ï¼è¿æ¯ææç°ä»£åºçâå¿å¤âç¹æ§ãæ¥ä¸æ¥ï¼æä»¬éè¦æ·»å å¯¹ä¸­é´ä»¶çæ¯æï¼è¿ä¹æ¯ä¸ä¸ªéå¸¸å¸¸è§åå¼ºå¤§çæ¦å¿µãæéå¸¸åæ¬¢ç`aiohttp`çä¸ä¸ªä¼ç¹æ¯åºç¨ç¨åºçå½å¨ææé©(ä¾å¦`on_startup`ã`on_shutdown`ã`on_cleanup`)ï¼æä»¥æä»¬å½ç¶ä¹åºè¯¥å°è¯å®ç°å®ã

## è·¯çº¿åæ°

ç®åï¼æä»¬ç`UrlDispatcher`éå¸¸ç²¾ç®ï¼å®ä½¿ç¨æ³¨åç url è·¯å¾ä½ä¸ºå­ç¬¦ä¸²ãé¦åï¼æä»¬éè¦çæ¯å¨æä»¬ç`resolve`æ¹æ³ä¸­æ·»å å¯¹å`/user/{username}`è¿æ ·çæ¨¡å¼çæ¯æãæä»¬è¿éè¦`_format_pattern`å©æï¼è´è´£ä»åæ°åå­ç¬¦ä¸²çæå®éçæ­£åè¡¨è¾¾å¼ãä½ å¯è½ä¹æ³¨æå°äºï¼æä»¬æå¦ä¸ä¸ªå©æ`_method_not_allowed`åæ¹æ³æ¥ç®å`GET`ã`POST`ç­çå®ä¹ãè·¯çº¿ã

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

æä»¬è¿éè¦ä¿®æ¹æä»¬çåºç¨ç¨åºå®¹å¨ï¼ç°å¨`UrlDispatcher`ç`resolve`æ¹æ³è¿å`match_info`å`handler`ãæä»¥å¨`Application._handler`éé¢ä¿®æ¹ä»¥ä¸å è¡ã

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

## ä¸­é´ä»¶

å¯¹äºé£äºä¸çæè¿ä¸ªæ¦å¿µçäººæ¥è¯´ï¼ç®åå°è¯´`middleware`åªæ¯ä¸ä¸ªåç¨ï¼å®å¯ä»¥ä¿®æ¹ä¼ å¥çè¯·æ±å¯¹è±¡ææ¹åå¤çç¨åºçååºãå®å°å¨æ¯æ¬¡åæå¡å¨ååºè¯·æ±ä¹åè¢«è§¦åãå¯¹äºæä»¬çéæ±æ¥è¯´ï¼å®ç°æ¯å¾®ä¸è¶³éçãé¦åï¼æä»¬éè¦å¨æä»¬ç`Application`å¯¹è±¡ä¸­æ·»å æ³¨åçä¸­é´ä»¶åè¡¨ï¼å¹¶ç¨å¾®ä¿®æ¹ä¸ä¸`Application._handler`æ¥è¿è¡å®ä»¬ãæ¯ä¸ªä¸­é´ä»¶é½åºè¯¥ä¸é¾ä¸­åä¸ä¸ªçç»æä¸èµ·å·¥ä½ã

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

ç°å¨è®©æä»¬å°è¯å°è¯·æ±æ¥å¿ä¸­é´ä»¶æ·»å å°æä»¬çç®ååºç¨ç¨åºä¸­ã

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

å¦ææä»¬å°è¯è¿è¡å®ï¼æä»¬åºè¯¥çå°ååºä¼ å¥è¯·æ±ç`Received request to /`æ¶æ¯ã

## App çå½å¨ææé©

ä¸ä¸æ­¥ï¼è®©æä»¬æ·»å å¯¹è¿è¡æäºåç¨çæ¯æï¼ä»¥ååºåå¯å¨ååæ­¢æå¡å¨è¿æ ·çäºä»¶ãè¿æ¯`aiohttp`éå¸¸ç®æ´çç¹å¾ãæè®¸å¤ä¿¡å·ï¼æ¯å¦`on_startup`ã`on_shutdown`ã`on_response_prepared`ç­ç­ï¼ä½æ¯ä¸ºäºæä»¬çéè¦ï¼è®©æä»¬å°½å¯è½ç®åï¼åªå®ç°`startup`ã&ã`shutdown`å©æã

å¨`Application`ä¸­ï¼æä»¬éè¦ä¸ºæ¯ä¸ªäºä»¶æ·»å å®éå¤çç¨åºåè¡¨ï¼å¹¶æä¾ gettersãç¶åå®éç`startup`å`shutdown`åç¨å¹¶æ·»å ç¸åºçè°ç¨ç»`run_app`å©æã

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

## æ´å¥½çä¾å¤

å¨è¿ä¸æ­¥ï¼æä»¬æ·»å äºå¤§é¨åæ ¸å¿ç¹æ§ï¼ä½æ¯ï¼æä»¬ä»ç¶ç¼ºå°å¼å¸¸å¤çãå³äº`aiohttp`çä¸ä¸ªå¾æ£çç¹æ§æ¯å®åè®¸ä½ åå¤çæ¬å° python å¼å¸¸ä¸æ ·å¤ç web å¼å¸¸ã
å®éè¿å®ç°`Exception`å`Response`ç±»æ¥å®æï¼è¿ä¹æ¯æä»¬æ³è¦ççæ­£çµæ´»çæºå¶ã

æä»¥ï¼é¦åè®©æä»¬åå»ºæä»¬çåºç±»`HTTPException`åä¸äºåºäºå®çå¸®å©å¨ï¼æä»¬å¯è½éè¦å`HTTPNotFound`ç¨äºæªè¯å«çè·¯å¾`HTTPBadRequest`ç¨äºç¨æ·ç«¯é®é¢å`HTTPFound`ç¨äºéå®åã

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

ç¶åæä»¬éè¦ç¨å¾®ä¿®æ¹ä¸ä¸æä»¬ç`Application._handler`æ¥å®éææ web å¼å¸¸ã

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

ç°å¨æä»¬ä¹å¯ä»¥ä»æä»¬ç`UrlDispatcher`ä¸­å é¤`_not_found` & `_method_not_allowed`å©æï¼åèä»£ä¹çæ¯æåºéå½çå¼å¸¸ã

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

å¦ä¸ä¸ªå¾å¥½çè¡¥åæ¯åé¨æå¡å¨éè¯¯çæ åæ ¼å¼ååºï¼å ä¸ºæä»¬ä¸æ³å¨ä¸äºä¸ä¸è´çæåµä¸ç ´åå®éçåºç¨ç¨åºãè®©æä»¬æ·»å ä¸ä¸ªç®åç html æ¨¡æ¿åä¸ä¸ªç¨äºæ ¼å¼åå¼å¸¸çå°å©æã

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

ç®åå¾å¾ï¼ç°å¨åªéæè·æä»¬ç`Application._handler`ä¸­çææ`Exception`ï¼å¹¶ä½¿ç¨æä»¬çå©æçæå®éç html ååºã

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

## ä¼éå³æº

æåï¼æä»¬éè¦ä¸ºå³é­åºç¨ç¨åºçæ­£ç¡®è¿ç¨æ·»å ä¿¡å·å¤çãæä»¥ï¼æä»¬æ`run_app`æ¹æä¸é¢å è¡ã

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

## æ ·æ¬åºç¨

ç°å¨ï¼å½æä»¬åå¤å¥½å·¥å·ååï¼è®©æä»¬è¯çå®ææä»¬ä¹åæ·»å çç¤ºä¾åºç¨ç¨åºçå½å¨ææé©åå¼å¸¸ã

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

å¦ææä»¬åäºæææ­£ç¡®çäºæï¼ä½ ä¼çå°æ¯ä¸ªè¯·æ±çæ¥å¿æ¶æ¯ï¼æ³¨åç¨æ·çæ¬¢è¿æ¶æ¯ï¼ä»¥åæªæ³¨åç¨æ·åæªè¯å«è·¯å¾ç`HTTPNotFound`ã

## ç»è®º

æ»ç»ä¸ä¸ï¼å¨å¤§çº¦ 500 è¡ä»£ç ä¸­ï¼æä»¬æå·¥å¶ä½äºä¸ä¸ªéå¸¸ç®åä½åè½å¼ºå¤§çå¾®æ¡æ¶ï¼çµææ¥èª`aiohttp` & `sanic`ãå½ç¶ï¼å®ä¸æ¯çäº§å°±ç»ªè½¯ä»¶ï¼å ä¸ºå®ä»ç¶ç¼ºå°è®¸å¤æç¨ç&éè¦åè½ï¼å¦æ´å¥å£®çæå¡å¨ãæ´å¥½ç HTTP æ¯æä»¥ä¸è§èå®å¨ç¸å³ãweb å¥æ¥å­ç­ç­ãç¶èï¼æç¸ä¿¡éè¿è¿ä¸ªè¿ç¨ï¼æä»¬å¯¹è¿äºå·¥å·æ¯å¦ä½æå»ºçæäºæ´å¥½ççè§£ãæ­£å¦èåç©çå­¦å®¶çæ¥å¾·Â·è´¹æ¼æè¯´ï¼âæä¸è½åé çä¸è¥¿ï¼æä¸çè§£âãæä»¥æå¸æä½ åæ¬¢è¿ä¸ªæåï¼åè§ï¼ð