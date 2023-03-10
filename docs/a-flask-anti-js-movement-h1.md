# 一场激烈的反 Js 运动

> 原文：<https://dev.to/michaelbukachi/a-flask-anti-js-movement-h1>

在过去的几个月里，我一直在使用 Flask 开发 API。这段时间，我注意到一个共同点。API 消费者(至少是我接触过的人)从来没有仔细阅读过提供的文档来熟悉需要什么。现在，我不确定这是有意的还是偶然的(需要反馈😄).使用你的 API 的开发者反复试验的结果可能会很烦人，尤其是如果你有像 [sentry](https://sentry.io/welcome/) 这样的错误报告服务。

想象一下，从你的错误报告服务中醒来，收到一大堆邮件，却发现这是错误输入的结果😥。使用 Flask 扩展，如 [Flask-Restplus](https://flask-restplus.readthedocs.io/en/stable/) 可以帮助您轻松管理错误输入。此外，**错误请求**异常很容易在错误报告服务中被忽略，这样它们就不会一直触发警报。然而，有一些路径变量会引入一种不同的问题。
看看下面的片段:

```
@app.route('/<string:user_id>')
def get_details(user_id):
.... # Do something with user_id 
```

您已经创建了一个端点，它从某个数据库中获取用户的详细信息，并返回必要的详细信息。您已经提供了关于该端点使用的适当文档，并且您对自己的工作非常满意。你决定早点去吃午饭，结果回来得很早，因为你刚要吃午饭就开始收到错误提示😩。

您快速检查日志和堆栈跟踪，看看发生了什么。所有的错误都来自你新创建的端点。

怎么会？
[![How?](img/7cc827c80bf79c003c0b43f85ed04362.png)T3】](https://i.giphy.com/media/3o7btPCcdNniyf0ArS/giphy.gif)

你很确定你添加了所有必要的检查。您甚至向 path 变量添加了类型检查，以防止无效类型。

仔细观察堆栈跟踪，您会注意到传递的 user_id 是一个未定义的值**。使用你的 API 的开发者可能正处于可怕的**试错**阶段，试图在前端把事情连接起来。**

 **现在，一个好的 API 应该被防止误用。是的，这包括使用你的 API 的开发者的试错行为。

为了处理“未定义”的变量，你可以添加一个条件检查并返回一个适当的响应。

```
@app.route('/<string:user_id>')
def get_details(user_id):
    if user_id == 'undefined':
        raise BadRequest('Invalid user id')
    # Do something with user_id .... 
```

然而，这种解决方案不能很好地扩展。如果您有数百个带有路径变量的端点会怎样？然后呢？

更好的解决方案是使用 Flask 的`before_request`装饰器，比如:

```
@app.before_request
def check_undefined_values():
    for variable in request.view_args.values():
        if variable == 'undefined':
            raise BadRequest('\'undefined\' value found.') 
```

这种方法最有效，因为在请求被转发到端点之前，总是调用`check_undefined_values`函数。

我创建了一个名为 [Flask-AntiJs](https://github.com/michaelbukachi/flask-antijs) 的 Flask 扩展，它使用了上面的方法。它有额外的功能，比如检查请求负载和查询参数中未定义的值的能力。这实际上是我的第一个烧瓶延长，所以检查一下😁。

今天到此为止。

[![Done](img/1290ed3ba31c847fba0031f47c30d357.png)](https://i.giphy.com/media/lD76yTC5zxZPG/giphy.gif)**