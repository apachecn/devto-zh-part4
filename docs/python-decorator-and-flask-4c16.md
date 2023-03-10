# Python 装饰器和烧瓶

> 原文：<https://dev.to/sonnk/python-decorator-and-flask-4c16>

装饰器是我最喜欢的 Python 特性之一。虽然一开始看起来很混乱，但它只是一个将另一个函数作为参数并返回另一个函数的函数。

`decorator: Func -> Func`

这是可能的，因为在 Python 中，`function`是一等公民，这意味着函数可以用作参数、返回值并可以赋给变量。

一旦理解了，`decorator`就可以用在各种场合，其中一些将在下面讨论。

根据定义，

> 装饰器是一个函数，它接受另一个函数并扩展后一个函数的行为，而不需要显式修改它

具体如下代码:

```
@decorator
def f(argument):
    ... 
```

Enter fullscreen mode Exit fullscreen mode

将`f`替换为`decorator(f)`:呼叫`f(argument)`则等同于`decorator(f)(argument)`。

Flask 是一个流行的轻量级 web 框架，它广泛使用 decorator 来生成优雅而直观的代码。装饰器的使用遍及整个框架，从定义路由到给应用程序/请求生命周期添加钩子。

在这篇小帖子中，将讨论 Flask 中装饰者的一些用例。请注意，这篇文章是为已经使用过 Flask 和 decorators 的用户准备的。代码示例也大多是伪代码，以说明想法，而不是立即可用。如果你想了解 Flask 或 decorator，你可以在帖子的末尾找到一些资源。

* * *

## 先说第一件事，@app.route 是如何实现的？

Flask 的第一个例子通常以如下开头:

```
@app.route(“/”)
def index():
    return "Hello world" 
```

Enter fullscreen mode Exit fullscreen mode

通过将`app.route`作为装饰者，函数`index`被注册用于路由`/`，这样当请求该路由时，`index`被调用，其结果“Hello world”被返回给客户端(可以是 web 浏览器、curl 等)。

你有没有想过无处不在的`@app.route`是怎么实现的？因为装饰器通常用于修改函数行为，所以当调用这个函数时，通常可以看到它的效果。而`index`在代码中没有被调用。

关于 decorator 一个不受欢迎的事实是，它是**定义时间而不是运行时间**，也就是说当模块**导入**时`index`将被`app.route('/')(index)`替换。事实上，这种注册是在定义时间内完成的，这一点很重要，如果不是这样，Flask 将无法知道索引的存在。

`@app.route`的实现逻辑实际上非常简单，基本上是将`index`添加到一个全局 URL-函数映射变量中，当有请求进来时，该变量将被搜索:

```
class App:
    route_functions = {}
    def route(url_pattern):
        def wrap(f):
            route_functions[url_pattern] = f
            return f
        return wrap 
```

Enter fullscreen mode Exit fullscreen mode

所以当用`app.route(“/”)(index)`代替`index`时，`index`被添加到包含路由功能匹配的`route_functions`中。

* * *

## 登录装饰者

假设您有一个 Flask 应用程序，允许用户添加推文。每个 tweet 属于一个用户，只有登录的用户可以添加 tweet。因此，您希望将某些路由限制为仅允许已登录的用户访问，如果用户未登录，他们将被重定向到登录页面。

为每条路线添加登录检查相当麻烦，并且违反了 **DRY** 原则。如果我们可以用@login_required 来修饰这样的路由，并且所有的登录检查都将自动完成，那就太方便了。

```
@app.route("/add_tweet")
@login_required
def add_tweet():
   ... 
```

Enter fullscreen mode Exit fullscreen mode

假设用户验证使用 cookie，`login_required`首先检查 cookie，如果 cookie 无效，重定向用户到登录页面。否则调用`add_tweet` :

```
from functools import wraps
def login_required(f):
    @wraps(f)
    def wrap(*args, **kwargs):
        # if user is not logged in, redirect to login page      
        if not request.headers["authorization"]:
            return redirect("login page")
        # get user via some ORM system
        user = User.get(request.headers["authorization"])
        # make user available down the pipeline via flask.g
        g.user = user
        # finally call f. f() now haves access to g.user
        return f(*args, **kwargs)

    return wrap 
```

Enter fullscreen mode Exit fullscreen mode

注意`login_required`需要放在`app.route`之后，这样当路线匹配时`login_required(add_tweet)`将被调用，而不仅仅是`add_tweet`。否则，如果`login_required`放在`app.route`之前，则在请求路线时仅调用`add_tweet`，因此登录要求检查无效。

* * *

## 基于角色的装饰者

前面的登录装饰器可以扩展到考虑各种用户角色。假设我们的管理员用户可以调用其他用户不能调用的路由。如果有一个装饰器`admin_login_required`就好了，当与`login_required`一起使用时，它会标记一条只对管理员用户可用的路线。

```
def admin_login_required():
    def wrap(*args, **kwargs):
        # user is available from @login_required
        if not g.user.is_admin:
            return "you need to be admin", 401
return f(*args, **kwargs) 
```

Enter fullscreen mode Exit fullscreen mode

因此，要装饰一个需要管理员访问的路由，只需添加这两个装饰者:

```
@app.route("/admin/delete_user")
@login_required
@admin_login_required
def admin_delete_user():
    ... 
```

Enter fullscreen mode Exit fullscreen mode

这里的顺序也很重要:`admin_login_required`必须放在`login_required`之后，才能从`login_required`中设置的`g.user`中受益。

* * *

## 其他用途

利用 decorator 可以优雅地解决各种干燥问题:

*   通过类型检查从 request query、JSON、form 获取输入，这样，如果输入丢失或类型不正确，则返回 400
*   添加运行时间记录，以了解哪些路线运行缓慢
*   基于 URL 参数的页面缓存
*   等等

* * *

### IMO 学习烧瓶和装饰师的一些优秀资源:

[https://blog . miguelgrinberg . com/post/the-flask-mega-tutorial-part-I-hello-world](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world)

[http://simeonfranklin . com/blog/2012/jul/1/python-decorators-in-12-steps/](http://simeonfranklin.com/blog/2012/jul/1/python-decorators-in-12-steps/)

[https://www . the deship . com/patterns/guide-to-python-function-decorators/](https://www.thecodeship.com/patterns/guide-to-python-function-decorators/)