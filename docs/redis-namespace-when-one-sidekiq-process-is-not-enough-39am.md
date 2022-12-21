# redis-namespace:当一个 sidekiq 进程不够用时

> 原文：<https://dev.to/rbglod/redis-namespace-when-one-sidekiq-process-is-not-enough-39am>

几个月前，我在一个项目中工作，我们有两个应用程序相互通信。他们每个人都必须有自己的 sidekiq 流程，但是他们被部署到一台服务器上。过了一段时间，我意识到在部署了一个应用程序之后，第二个应用程序在调用它的 workers 时出现了问题。问题是 sidekiq 只从一个(最后部署的)应用程序重启和加载工人。所以每次部署后我都有很多错误说

```
NameError: uninitialized constant AppOneWorker 
```

还有一次

```
NameError: uninitialized constant AppTwoWorker 
```

随着营救而来的是名为`redis-namespace`的宝石。

### [再命名空间](https://github.com/resque/redis-namespace)

如其回购说明中指出，`redis-namespace` :

```
...adds a Redis::Namespace class which can be used to namespace Redis keys. 
```

这是什么意思？这意味着我们可以使用一个 Redis 服务器运行大量的应用程序，并且许多 sidekiq 进程不会相互冲突。

你所需要做的就是将`redis-namespace`添加到你的 Gemfile 中，并在`app/config/initializers/sidekiq.rb`中配置它，如下所示:

```
# frozen_string_literal: true

require 'sidekiq'

Sidekiq.configure_server do |config|
  config.redis = {
    url: 'redis://127.0.0.1:6379',
    namespace: 'my_first_app_name'
  }
end

Sidekiq.configure_client do |config|
  config.redis = {
    url: 'redis://127.0.0.1:6379',
    namespace: 'my_first_app_name'
  }
end 
```

对第二个应用程序做同样的事情(当然，要有正确的名称)。

之后，当你访问你的应用程序的`/sidekiq`路径时(如果你在`routes.rb`中挂载了它)，你可以看到 sidekiq 进程在它的名字前面添加了名称空间。
检查一切是否正常的另一种方法是在命令行中启动`redis-cli`，并查找包含您的名称空间的键。

```
redis 127.0.0.1:6379> keys *app_name*
 1) "my_first_app_name:queues"
 2) "my_second_app_name:queues" 
```

### 学分

当我遇到这个问题时，如果没有 Maciej Mensfeld 的关于 redis-namespace 的帖子，我是不会解决这个问题的。谢谢！

你甚至可以在那里找到我的热情评论，我当时就离开了，哈哈。