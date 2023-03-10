# 用 Python 集成测试 websockets

> 原文：<https://dev.to/gridsmartercities/integration-testing-websockets-with-python-40jc>

TL；灾难恢复测试很重要，但也很困难。我已经编写了 Python 库 pywsitest 来帮助集成测试 websockets，并提供了一些例子。

在 [Grid Smarter Cities](https://github.com/gridsmartercities) 我们相信测试是优秀软件的关键组成部分。

我们对测试的态度主要反映在我们的开发过程中，我们实践测试驱动开发，使用 [Prospector](https://prospector.readthedocs.io/en/master/) 执行代码分析，并要求 100%的测试覆盖率(我们使用 [Coverage.py](https://coverage.readthedocs.io/en/v4.5.x/) 来检查)。这个过程在我们的自动化构建管道中重复，它运行林挺，针对我们的代码的全套单元测试，以及针对阶段环境的集成测试。

我们关于测试的许多想法大致基于测试金字塔(见下文)，因为单元测试写起来快，运行起来也快，所以它们构成了我们使用的大多数测试。集成测试需要更长的时间来运行，通常在运行一个实时系统时会产生一些成本，并且编写起来更复杂；所以我们很少写它们，但它们仍然是确保我们有好产品的重要部分。

UI 测试和手动测试都是更耗时和更昂贵的过程，所以它们只占我们测试的一小部分。因为作为一名后端开发人员，我不必太担心这些问题，所以我今天会稍微跳过它们，尽管我很欣赏完整测试套件和过程的重要性。

[![Test pyramid](img/f2a2cf84768999e9e131b4d3cff61a72.png)](https://speakerdeck.com/slobodan/designing-testable-serverless-apps-using-hexagonal-architecture?slide=56)

无论我们编写了多少单元测试，无论我们可以多么独立地信任我们的代码，我们都不能对我们的产品有充分的信心，除非我们也在一个真实的系统上测试模块之间的交互。REST APIs 的自动化集成测试是一个相当直接的过程，使用类似于 [Dredd](https://dredd.org/en/latest/) 的工具以及 OpenAPI 模板，但是 Grid 在测试我们与基于 websocket 的 API 的一些更复杂的交互时遇到了困难。

## pywsitest

我们最近在 Grid 开发的一个产品要求不同的用户实时地与一个请求对象交互并更新它。我们选择对应用程序的所有异步部分使用基于 websocket 的 API。

当每个用户类型连接到 websocket 主机时，他们会收到一组关于他们当前请求状态、其他活动用户和一些基于地理位置的数据的消息。当一个用户修改请求对象时，某些其他用户会收到状态更新，并且用户可以对请求执行的操作受到限制。

测试用户之间的异步交互成为一项挑战，这也是我们创建 Python 库[pywsitest](https://github.com/gridsmartercities/pywsitest)(Python web socket 集成测试框架)的原因。

pywsitest 库允许用户连接到 websocket 主机，断言已经收到一系列消息，并且任何需要发送的消息都已成功发送。消息可以通过连接发送，也可以通过接收任何指定的消息来触发。这种监听响应并在收到消息时发送出去的能力允许编写针对不同用户之间交互的测试。

## 测试场景

我写了一个基于 websocket 的聊天服务器，允许用户使用用户名连接，通过服务器向所有连接的用户发送消息，并在他们完成后断开与服务器的连接。

用户名将作为查询参数包含在 url 中。因为我在本地运行这个服务器进行测试，所以我将使用 url 来连接。

当用户连接到服务器时，服务器会向所有连接的用户广播一条消息，告诉他们用户已经连接:

```
{  "message":  "Rob has connected"  } 
```

Enter fullscreen mode Exit fullscreen mode

当用户向服务器发送消息时，该消息将以如下格式广播给所有连接的用户:

```
{  "message":  "Rob: Hello, world!"  } 
```

Enter fullscreen mode Exit fullscreen mode

当用户断开连接时，服务器将向所有连接的用户广播:

```
{  "message":  "Rob has disconnected"  } 
```

Enter fullscreen mode Exit fullscreen mode

对于我的第一个测试，我想确保用户可以连接到 websocket 主机，并断言他们收到了一个广播消息，表明他们已经连接。

为此，我设置了一个指向正确 url 的`WSTest`对象，然后添加我的名字作为查询参数。因为我们期待来自主机的消息，让我们知道我们已经成功连接，所以我在`WSTest`实例上使用`with_response`方法添加了一个预期响应。传递给`with_response`方法的是`WSResponse`的一个实例，它有一个与我期望从主机接收的 json 消息相匹配的属性。

```
import asyncio
from pywsitest import WSTest, WSResponse

ws_test = (
    WSTest("wss://localhost")
    .with_parameter("name", "Rob")
    .with_response(
        WSResponse()
        .with_attribute("message", "Rob has connected")
    )
)

asyncio.get_event_loop().run_until_complete(ws_test.run())

assert ws_test.is_complete() 
```

Enter fullscreen mode Exit fullscreen mode

因为`run`方法是异步的，所以我使用这个例子中的 [`asyncio`](https://docs.python.org/3/library/asyncio.html) 库同步运行它，并带有一行代码:

```
asyncio.get_event_loop().run_until_complete(ws_test.run()) 
```

Enter fullscreen mode Exit fullscreen mode

我想测试的下一个功能是用户在连接到 websocket 主机后发送消息的能力。该测试的设置方式与上一个测试非常相似，但是在收到连接消息时会触发一条消息。测试运行程序还将期待来自主机的响应，显示测试运行程序发送的消息。

```
import asyncio
from pywsitest import WSTest, WSResponse, WSMessage

ws_test = (
    WSTest("wss://localhost")
    .with_parameter("name", "Rob")
    .with_response(
        WSResponse()
        .with_attribute("message", "Rob has connected")
        .with_trigger(
            WSMessage()
            .with_attribute("message", "Hello, world!")
        )
    )
    .with_response(
        WSResponse()
        .with_attribute("message", "Rob: Hello, world!")
    )
)

asyncio.get_event_loop().run_until_complete(ws_test.run())

assert ws_test.is_complete() 
```

Enter fullscreen mode Exit fullscreen mode

我想演示的最后一个功能是测试两个同时运行的客户端。为此，我将创建运行每个测试的方法，并使用`asyncio.gather()`同时运行这两个方法。我还将在其中一个方法中添加一个短暂的延迟，以帮助确保在其中一个用户广播消息之前，两个用户都已经连接。

在这个测试中，`user_1`将连接到 websocket 主机，并等待直到它接收到`user_2`已经连接的广播消息，然后发送消息。然后，`user_2`将回复该消息，此时，`user_1`的测试将结束，`user_2`将收听广播消息，通知每个人`user_1`已断开连接。

```
import asyncio
from pywsitest import WSTest, WSResponse, WSMessage

async def test_user_1():
    ws_test_1 = (
        WSTest("wss://localhost")
        .with_parameter("name", "user_1")
        .with_response(
            WSResponse()
            .with_attribute("message", "user_1 has connected")
        )
        .with_response(
            WSResponse()
            .with_attribute("message", "user_2 has connected")
            .with_trigger(
                WSMessage()
                .with_attribute("message", "Hello from user_1!")
            )
        )
        .with_response(
            WSResponse()
            .with_attribute("message", "user_2: Hello from user_2!")
        )
    )

    await ws_test_1.run()
    assert ws_test_1.is_complete()

async def test_user_2():
    await asyncio.sleep(2)

    ws_test_2 = (
        WSTest("wss://localhost")
        .with_parameter("name", "user_2")
        .with_response(
            WSResponse()
            .with_attribute("message", "user_2 has connected")
        )
        .with_response(
            WSResponse()
            .with_attribute("message", "user_1: Hello from user_1!")
            .with_trigger(
                WSMessage()
                .with_attribute("message", "Hello from user_2!")
            )
        )
        .with_response(
            WSResponse()
            .with_attribute("message", "user_1 has disconnected")
        )
    )

    await ws_test_2.run()
    assert ws_test_2.is_complete()

async def run_tests():
    await asyncio.gather(test_user_1(), test_user_2())

asyncio.get_event_loop().run_until_complete(run_tests()) 
```

Enter fullscreen mode Exit fullscreen mode

## 期末备注

编写 pywsitest 的目的是帮助 Grid Smarter Cities 更好地测试我们的产品，并简化针对基于 websocket 的服务编写集成测试的过程。

这是一个开源项目，所以如果你想参与未来的开发，这将是非常感谢！