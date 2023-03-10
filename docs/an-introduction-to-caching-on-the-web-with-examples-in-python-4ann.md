# 关于网络缓存的介绍，包括 Python 中的例子

> 原文：<https://dev.to/healeycodes/an-introduction-to-caching-on-the-web-with-examples-in-python-4ann>

## 什么是缓存？

缓存存储数据，以便将来的请求可以更快地接收数据。在动态 web 应用程序中生成一个组件或视图的成本可能很高，尤其是在涉及数据库调用时。将计算结果保存到缓存中意味着为相同数据的下一个请求提供服务将更加高效。

Web 应用程序可以使用运行在同一 web 服务器上的缓存、远程缓存或分布式系统。云服务公司为高度可扩展的分布式缓存提供 API。在简单的单线程应用程序中，您甚至可以使用一个对象作为缓存！我在 tiny Node.js 服务中看到过这种用法。

## 好吧，给我举个例子

你可能听说过 Memcached 或者 Redis。它们都允许键/值存储以及在其他数据结构中存储数据。下面是缓存使用的一个高级示例。假设我们的 web 应用程序在`/books/312`收到一个请求，其中 312 是图书实体的 id。我们希望返回一个动态页面，其中包含关于这本书的详细信息，也许还有人们对这本书的一些评论。

在伪代码中，将缓存应用到这种情况看起来像这样:

```
do we have key '/books/312' in our cache?
if so:
    return that value as a response
else:
    generate a page
    store page in cache at '/books/321/'
    return page as a response 
```

在这个例子中，获取图书 321 的详细信息需要调用我们的数据库。获取最近的评论也可能涉及单独的数据库调用。然后，我们可以通过服务器端模板生成页面。当用户在最初请求后不久访问同一页面时，所有这些计算都可以避免。

当我们可以用不同的方法找到正确的信息时，应该避免数据库调用。本地缓存以亚毫秒级的响应时间运行。缓存填满时也会删除最后使用的项目。总的来说，它们很难，但是设置一个合理的超时值是很重要的。

Memcached 可以使用不互相通信的分布式服务器来部署。只有客户端(我们的 web 应用程序)知道缓存服务器。与哈希表的操作非常相似，对密钥进行哈希运算，然后选择一个服务器来获取/设置密钥。

这里有一个高级的例子:

```
call a hash function on 'books/321'
turn the hashed value into an index
check the corresponding cache server
get or set 'books/321/' from this server 
```

web 应用程序知道的缓存服务器的数量是无限的。它可以水平扩展，这意味着可以添加更多的服务器来处理更高的负载。

以下是您在使用缓存系统时可能会遇到的一些问题:

*   用户接收旧数据
*   缓存填满得太快
*   请求过于独特，无法缓存
*   竞赛条件

## 一个种族的条件？

这里有一个涉及缓存的竞争条件的例子。我们想在我们的网站上显示一个旧的学校点击计数器。当用户请求页面时，我们将检查当前的点击次数，将它加 1，并覆盖这个键。

理想情况下，它是这样工作的:

```
get 'hit_count' from cache
increase by one
save new 'hit_count' to cache 
```

如果我们的 web 应用程序是多线程的或以某种方式分布的，缓存可以同时被命中两次。两个服务器实例都增加了从缓存中接收的命中次数。然后，两个服务器实例都将该值保存回来。从缓存的角度来看，一个键被请求两次，然后保存两次。web 应用程序的两个实例都请求当前的命中值`10`，加一，这样它们都有`11`，然后它们将该值保存到缓存中。真实点击数是`12`，一些数据已经丢失！

这个问题实际上是由 Memcached 解决的，它提供了递增和递减功能。对于需要原子操作的更复杂的情况，Memcached 具有比较和设置特性。其中使用重试循环来尝试执行操作，而不会丢失任何数据。

这里有一篇[很棒的博文](http://neopythonic.blogspot.com/2011/08/compare-and-set-in-memcache.html)更详细地介绍了 CAS。

## 给我代码中的例子

让我们看一些真实代码中的例子。我们将使用 Flask 和 Django，这是最流行的 Python web 框架。两者都有强大的开箱即用的解决方案。

Flask 建立在 Werkzeug 之上，这意味着我们可以使用来自`werkzeug.contrib.cache`的 API，而不需要包含任何额外的模块。Flask 有一个关于缓存模式的页面[就在这里](http://flask.pocoo.org/docs/1.0/patterns/caching/)。

这是我最近写的一个原型应用的一些代码，并添加了评论

```
# SimpleCache is a dev tool and is not threadsafe
# but it's very easy to swap in MemcachedCache or GAEMemcachedCache from werkzeug.contrib.cache import SimpleCache

def create_app():
    app = Flask(__name__)
    # this line could be `cache = MemcachedCache(['127.0.0.1:11211'])`
    cache = SimpleCache()

    @app.route('/nearby/<postcode>/<radius>')
    def nearby_stores(postcode, radius):
        # have we performed this calculation for these arguments?
        if cache.get('{}{}'.format(postcode, radius)) is not None:
            return cache.get('{}{}'.format(postcode, radius))

        # otherwise do some calculations
        # .. 
        # store for next time we run with these arguments
        cache.set('{}{}'.format(postcode, radius), jsonified)
        return jsonified 
```

这个应用程序中的缓存检查可以节省处理、数据库调用和一个第三方请求！然而，使用来自 Werkzeug 的缓存模块确实会增加一些不必要的行，像`Flask-Cache`这样的扩展会去掉这些行。我们可以使用一个使用`request.path`(可配置)的装饰器，而不是手动检查缓存和设置缓存。

所以这个函数的开始应该是这样的:

```
 @app.route('/nearby/<postcode>/<radius>')
    @cache.cached(timeout=120)
    def nearby_stores(postcode, radius): 
```

# Django 呢？

像 Flask 一样，Django 也有一个关于为应用程序设置缓存的精彩页面。它的缓存框架与我们刚刚看到的情况非常相似。最大的区别将是初始配置。Django 还可以将缓存的数据保存在正在使用的数据库中。配置也比 Flask 更成熟。

下面是他们文档中的一个例子:

```
from django.views.decorators.cache import cache_page

@cache_page(60 * 15)
def my_view(request): 
```

# 最难的部分

构建您的应用程序并找出哪些计算可以保存并在以后使用是最困难的部分！为简单的 web 应用程序缓存 web 请求是一个相当容易解决的问题。如何使用你的宝刀取决于你自己。

* * *

我在我的每周[简讯](https://buttondown.email/healeycodes)上发布独特的内容📧！

并且发关于科技的推特 [@healeycodes](https://twitter.com/healeycodes) 。