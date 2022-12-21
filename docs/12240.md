# 使用 Memcache 和 Python 的多租户缓存解决方案

> 原文：<https://dev.to/juampilorenzo/a-multi-tenant-cache-solution-with-memcache-and-python-287d>

你好！这是我在这个网站的第一篇技术文章。我想写一些关于我正在做的事情，甚至这个解决方案试图克服一个单一的用例，这对我非常有用。

## 用例

在为基于云的解决方案开发微服务时，我发现需要使用一个 *Memcached* 集群来缓存一些请求响应。简单地说，我发现我在使用 *flask-caching* (基于 *Werkzeug* 缓存模块)和 *Memchached* 缓存时有两个问题:

1.  当您在代码中创建 MemcachedCache 客户端时，可以使用键前缀来分隔集群中不同的应用程序缓存。但令我惊讶的是，这并不意味着您可以在不影响其他应用程序的情况下清除缓存。如果你做 *clear()* ，整个缓存都是死的。- > **不太多应用**
2.  在应用程序中缓存来自不同租户的资源的唯一方法是在键中包含租户。这是合乎逻辑的。但是，这并不意味着您能够删除应用程序缓存的一部分。即使您的集群仅用于您的应用程序，如果您的一个租户更新了它的数据，并且您需要使它的缓存无效，那么存储在缓存中的所有数据都将丢失。- > **不是很多租户**

## 解

我们可以用几行应用程序代码来解决这两个问题。我想到分叉 *flask-caching* 库，以库代码的形式提供解决方案。但我认为为此创建另一个库是不值得的，如果您需要用例，您可以在应用程序端添加逻辑。

免责声明:我将使用多租户的例子。对于多应用程序用例，您可以做完全相同的事情。另外，你可以同时做这两件事。

我们将基于一个简单的概念:我们将在缓存中为每个租户存储一个名称空间值。

```
def get_namespace_value(tenant_id):
    """
    Get namespace current value.
    :param tenant_id: tenant id
    :return: namespace current value.
    """
    stored_namespace = CACHE.get("namespace:{}".format(tenant_id))
    namespace_value = int(stored_namespace) if stored_namespace is not None else 0
    CACHE.set("namespace:{}".format(tenant_id), namespace_value, timeout=TIMEOUT)
    return namespace_value 
```

当我们第一次需要在缓存中存储一些东西时，我们将把名称空间的值设置为 0，并将这个值与键相加。

```
def set_cache(tenant_id, key, value):
    """
    Sets data to cache.
    :param tenant_id: tenant id
    :param key: key to use for caching
    :param value: value to cache
    :return:
    """
    CACHE.set("{}:{}/{}".format(
        tenant_id, get_namespace_value(tenant_id), key
    ), value, timeout=TIMEOUT) 
```

每次我们需要一个键时，我们将首先获取名称空间的值，并将其添加到查询中。

```
def get_cache(tenant_id, key):
    """
    Get data from cache
    :param key: key used for caching
    :param tenant_id: tenant id
    :return: tenant data in cache or None if empty
    """
    return CACHE.get("{}:{}/{}".format(
        tenant_id, get_namespace_value(tenant_id), key
    )) 
```

如果我们需要使租户的缓存无效，会发生什么呢？我们在名称空间值上加 1。

```
def clear_cache(tenant_id):
    """
    Clear cache in Memcache only for tenant data.
    Using a incremental namespace value avoid using .clear(),
    that flushes the complete cache.
    :param tenant_id: tenant id
    :return:
    """
    namespace_value = get_namespace_value(tenant_id)
    CACHE.set("namespace:{}".format(tenant_id), namespace_value+1, timeout=TIMEOUT) 
```

## 自己试试

### 要求

*   [码头工人](https://www.docker.com/)。
*   Python 2/3 具有以下要求:

```
python-memcached==1.59
Werkzeug==0.15.5 
```

### 步骤

1.  您可以使用 Docker 映像轻松尝试这个解决方案。您将拥有一个运行的内存缓存服务器，并通过您的 11211 端口进行监听。

```
sudo docker run -itd --name memcached -p 11211:11211 -e MEMCACHED_MEMUSAGE=32 rbekker87/memcached:alpine 
```

1.  您可以使用包含全部代码和一个简单演示的要点。