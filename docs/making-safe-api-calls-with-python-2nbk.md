# 使用 Python 进行安全的 API 调用

> 原文：<https://dev.to/michaelbukachi/making-safe-api-calls-with-python-2nbk>

在 python 中进行 REST API 调用是非常常见和容易的，特别是由于存在库，例如[请求](http://docs.python-requests.org/en/master/)。

举下面这个例子:

```
def get_user(user_id):
    res = requests.get(f'https://jsonplaceholder.typicode.com/users/{user_id}')
    if res.ok:
        return res.json()
    else:
        print(f'{res.status_code}: {res.text}')
        return {} 
```

代码非常简单。我们正在定义一个函数来从一个 [API](https://jsonplaceholder.typicode.com) (一个用于测试 web 应用的非常方便的 web 应用)中获取用户的详细信息。

简单。对吗？

然而，当没有连接时会发生什么呢？会引发异常。这可以很容易地包装在一个`try...except`子句中，但是这会使我们的代码变得混乱，特别是当我们有多个端点要调用的时候。

回车[返回](https://returns.readthedocs.io/en/latest/index.html)。

[![Grand entrance](img/236db78a5dbc05196b5da22de120edbc.png)](https://i.giphy.com/media/3oFzmpW5fjL3P45CXC/giphy.gif)

Returns 是一个考虑到安全性而编写的库。功能相当强大，大有可为。现在让我们快速修复我们的*不安全*代码。

```
class GetUser(object):

    @pipeline
    def __call__(self, user_id: int) -> Result[dict, Exception]:
        res = self._make_request(user_id).unwrap()
        return self._parse_json(res)

    @safe
    def _make_request(self, user_id: int) -> requests.Response:
        res = requests.get(f'https://jsonplaceholder.typicode.com/users/{user_id}')
        res.raise_for_status()
        return res

    @safe
    def _parse_json(self, res: requests.Response) -> dict:
        user = res.json()
        return user

get_user = GetUser() 
```

我们已经定义了一个一旦实例化就可以作为函数调用的类。我们需要注意到`@safe`装饰器，这是一个非常方便的装饰器，它将引发的任何异常封装到一个`Failure`对象中。
`Failure`类是`Result`类型的子类，基本上是一个简单的容器。
现在，我们的代码从:

```
try:
    user = get_user(1)
except Exception:
    print('An error occurred') 
```

致:

```
user = get_user(1) # returns Success(dict) or Failure(Exception) user = user.value_or({}) 
```

这难道不优雅吗？

我知道你在想什么。代码太多了！我一开始也是这么想的。但是在使用了函数库和一些定制之后，我开始注意到我的代码看起来更整洁了。我可以快速浏览一个文件，得到其中的要点，而不需要参考文献。这只是表面。Returns 还能提供更多。检查一下[出](https://returns.readthedocs.io/en/latest/index.html)。

我想到了一个装饰器来自动打开`Result`内部值或者提供一个默认值:

```
def unwrap(success=None, failure=None):
    def outer_wrapper(func):
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            result = func(*args, **kwargs)
            if is_successful(result):
                if success is not None:
                    return success
            else:
                if failure is not None:
                    return failure
            return result._inner_value

        return wrapper

    return outer_wrapper 
```

用法:

```
 @unwrap(failure={}) # If a failure occurs an empty dict is returnd
    @pipeline
    def __call__(self, user_id: int) -> Result[dict, Exception]:
        ...

user = get_user(1) # returns dict or empty dict 
```

干杯！