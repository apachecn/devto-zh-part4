# Phalcon v4.0.0-rc1 发布

> 原文：<https://dev.to/niden/phalcon-v4-0-0-rc1-released-4kjm>

我们非常高兴地宣布 **v4.0.0 发布候选(RC) 1** 的发布！。

我们对你们的社区感激不尽！我们以前说过这一点，但它值得重复:我们的社区是推动这个项目前进的动力，并使它随着每个版本而变得更好！

[Github 标签](https://github.com/phalcon/cphalcon/releases/tag/v4.0.0-rc.1)

随着我们越来越接近 v4 的稳定版本，这个版本已经解决了许多悬而未决的问题，但也发现了测试期间的错误。对于 v4，我们只有一个实际问题需要解决，还有 33 个文档需要修改。

你可以在我们的[项目](https://github.com/phalcon/cphalcon/projects/3)页面中看到 v4 的剩余卡片。

关于文件，你可以查看我们在这个问题上的进展:

[https://github.com/phalcon/docs/issues/2322](https://github.com/phalcon/docs/issues/2322)

> 在这个版本中，我们使用 Zephir 0.12.4，它提供了更严格的类型检查。结果，我们发现了许多类的接口错位，并修复了它们。

我们还在努力将我们所有的示例应用程序升级到 v4。Vokuro 已经接近完成(还有一些小的改进，比如代码覆盖率)，而 [phalcon-api](https://github.com/phalcon/phalcon-api) 的工作已经开始。

## 变更日志

### 补充道

*   向`Phalcon\Http\Request::getURI()`(及其接口)添加了可选的布尔参数，该参数指示该方法是否应该只返回路径而不返回查询字符串

### 变了

*   更改`Phalcon\Url::get`以使用`Phalcon\Helper\Str::reduceSlashes`后面的实现来减少斜线 [#14331](https://github.com/phalcon/cphalcon/issues/14331)
*   更改`Phalcon\Http\Headers\set()`以返回自我，获得更流畅的界面
*   将`Phalcon\Assets\Manager`、`Phalcon\Cli\Router`、`Phalcon\Dispatcher\AbstractDispatcher`、`Phalcon\Html\Tag`、`Phalcon\Http\Cookie`、`Phalcon\Http\Request`、`Phalcon\Http\Response\Cookies`、`Phalcon\Mvc\Model`、`Phalcon\Mvc\Router`、`Phalcon\Security`、`Phalcon\Session\Manager`更改为使用`Phalcon\Di\AbstractDiAware`实现容器功能 [#14351](https://github.com/phalcon/cphalcon/pull/14351)
*   更改`Phalcon\Security`以允许`session`和`request`在构造器 [#14351](https://github.com/phalcon/cphalcon/pull/14351) 中
*   更改`Phalcon\Session\Manager`以允许`router`在构造器 [#14351](https://github.com/phalcon/cphalcon/pull/14351) 中
*   更改`Phalcon\Flash\Direct`以允许`escaper`在构造器 [#14349](https://github.com/phalcon/cphalcon/issues/14349) 中
*   更改`Phalcon\Flash\Session`以允许`escaper`在构造器 [#14349](https://github.com/phalcon/cphalcon/issues/14349) 中
*   将`Phalcon\Di\AbstractDIAware`变更为`Phalcon\Di\AbstractInjectionAware` [#14359](https://github.com/phalcon/cphalcon/issues/14359)
*   更改了`Phalcon\Di\Service`以在可能的情况下使用 DI 来初始化基于`string`的服务 [#14342](https://github.com/phalcon/cphalcon/pull/14342)
*   更改`Phalcon\Mvc\Router\Annotations`以能够处理模式 az 前缀 [#14259](https://github.com/phalcon/cphalcon/pull/14259)
*   将`Phalcon\Mvc\Router\Group::routes`更改为默认的数组 [#14259](https://github.com/phalcon/cphalcon/pull/14259)
*   更改`Phalcon\Mvc\Model::assign`将参数顺序更改为$data，$whiteList，$dataColumnMap [#14386](https://github.com/phalcon/cphalcon/pull/14386)
*   将`Phalcon\Helper\Arr::arrayToObject`变更为`toObject` [#14389](https://github.com/phalcon/cphalcon/pull/14389)
*   将`Phalcon\Events\EventsAwareInterface::getEventsManager`和`Phalcon\Di\Injectable::getEventsManager`改为还`null` [#14404](https://github.com/phalcon/cphalcon/pull/14404)
*   将`Phalcon\Logger\Adapter\AbstractAdapter::add`改为现在返回`this` [#14404](https://github.com/phalcon/cphalcon/pull/14404)
*   更改了`Phalcon\Mvc\RouterInterface`方法签名以返回`RouteInterface`而不是`void`:
    *   `Phalcon\Mvc\RouterInterface::setDefaultAction`
    *   `Phalcon\Mvc\RouterInterface::setDefaultAction`
    *   `Phalcon\Mvc\RouterInterface::setDefaultController`
    *   `Phalcon\Mvc\RouterInterface::setDefaultModule`
    *   `Phalcon\Mvc\RouterInterface::setDefaults`
*   更改了以下接口的返回类型:
    *   `Phalcon\Mvc\Router\RouteInterface::setName`从`void`到`RouteInterface`
    *   `Phalcon\Mvc\Router\RouteInterface::via`从`void`到`RouteInterface`
    *   `Phalcon\Session\ManagerInterface::__get`从`void`到`var`

### 固定

*   修正了`Phalcon\Helper\Str::includes`返回正确结果 [#14301](https://github.com/phalcon/cphalcon/issues/14301)
*   修正了`Phalcon\Logger`移动到正确的名称空间 [#14263](https://github.com/phalcon/cphalcon/issues/14263)
*   修复了当`Session/Adapter/*::get()`返回空值 [#14314](https://github.com/phalcon/cphalcon/issues/14314) 时`Phalcon\Session\Adapter\AbstractAdapter::read()`返回" "(空字符串)
*   修复了`Phalcon\Cache\Exception`扩展 Phalcon \异常
*   修复了`Phalcon\Cache\InvalidArgumentException`扩展 Phalcon \异常
*   修复了`Phalcon\Collection\Exception`扩展 Phalcon \异常
*   修正了`Phalcon\Storage\Adapter\AbstractAdapter::initSerializer`如果`null === $this->serializerFactory && null === $this->serializer` [#14324](https://github.com/phalcon/cphalcon/issues/14324) 抛出异常
*   修复了`Phalcon\Storage\Adapter\Redis::getAdapter()`为 redis 持久连接提供一个持久 id[# 14334](https://github.com/phalcon/cphalcon/issues/14334)
*   修复了`Phalcon\Session\Adapter\Stream`不覆盖配置的保存路径 [#14265](https://github.com/phalcon/cphalcon/issues/14265)
*   修正了`Phalcon\Http\Response::setFileToSend`正确处理非 ASCII 文件名 [#13919](https://github.com/phalcon/cphalcon/issues/13919)
*   固定`Phalcon\Security::getSessionToken`返回值 [#14346](https://github.com/phalcon/cphalcon/issues/14346)
*   修正了`message()`返回`string/null` [#14349](https://github.com/phalcon/cphalcon/issues/14349) 的`Phalcon\Flash\*`接口
*   修正了`limit <= 0` [#14303](https://github.com/phalcon/cphalcon/issues/14303) 时抛出异常的问题
*   修复了查询 [#14366](https://github.com/phalcon/cphalcon/issues/14366) 中的`Phalcon\Mvc\Model\Query\Builder`空表别名
*   修正了`Phalcon\Db\Adapter\PdoFactory`以引用正确的接口 [#14381](https://github.com/phalcon/cphalcon/pull/14381)
*   已修复`Phalcon\Db\Dialect\Mysql`已修复创建表 [#14378](https://github.com/phalcon/cphalcon/issues/14378) 的约束中缺失的模式
*   修正了`castOnHydrate`选项开启时`Phalcon\Mvc\Model::hasChanged()`和`getChangedFields()`返回错误值的问题。 [#14376](https://github.com/phalcon/cphalcon/issues/14376)
*   修复了`Phalcon\Mvc\Model::create()`使用写连接来防止副本延迟 [#14256](https://github.com/phalcon/cphalcon/issues/14256)
*   满足接口声明的以下方法的固定返回类型:
    *   `Phalcon\Acl\Adapter\AbstractAdapter::setDefaultAction`
    *   `Phalcon\Application\AbstractApplication::setEventsManager`
    *   `Phalcon\Firewall\Adapter\AbstractAdapter::setAlwaysResolvingRole`
    *   `Phalcon\Firewall\Adapter\AbstractAdapter::setEventsManager`
    *   `Phalcon\Mvc\Router::handle`
    *   `Phalcon\Storage\Serializer\AbstractSerializer::getData`
    *   `Phalcon\Storage\Serializer\AbstractSerializer::setData`
    *   `Phalcon\Mvc\Dispatcher::forward`
    *   `Phalcon\Mvc\Model::setConnectionService`
    *   `Phalcon\Mvc\Model::setReadConnectionService`
    *   `Phalcon\Mvc\Model::setWriteConnectionService`
    *   `Phalcon\Mvc\Model\Query\Builder::setDI`
    *   `Phalcon\Mvc\Router\Annotations::handle`
    *   `Phalcon\Session\Bag::set`
    *   `Phalcon\Session\Manager::remove`
*   修复了`Phalcon\Di::remove()`移除服务。 [#14396](https://github.com/phalcon/cphalcon/issues/14396)

### 删除

*   删除`Phalcon\Plugin`—`Phalcon\DI\Injectable`[# 14359](https://github.com/phalcon/cphalcon/issues/14359)的副本
*   删除了`Phalcon\Mvc\Collection`和所有相关参考。4.0 将不支持 mongo，因为它正在被重新实现以利用最新的 Mongo 驱动程序(见 [#13697](https://github.com/phalcon/cphalcon/issues/13697) ) [#14361](https://github.com/phalcon/cphalcon/pull/14361)
*   删除了`Phalcon\Session\Manager::registerHandler` -重复功能 [#14381](https://github.com/phalcon/cphalcon/pull/14381)
*   删除了`Phalcon\Html\Tag` -重复功能 [#14381](https://github.com/phalcon/cphalcon/pull/14381)
*   移除了所有构造函数的返回类型 [#14401](https://github.com/phalcon/cphalcon/pull/14401)

## 安装/升级

在 [packagecloud.io](https://packagecloud.io/phalcon) 中的包正在更新中(在这篇文章发表的时候),很快就会准备好。您将需要使用`mainline`存储库来安装 v4.0.0-rc1。你也可以从我们的发布页面[这里](https://github.com/phalcon/cphalcon/releases/tag/v4.0.0-rc.1)下载 zip 文件以及 Windows 的 dll。

您还可以克隆存储库并检查标签，然后运行

```
zephir fullclean
zephir build 
```

安装新的扩展。详细的安装说明可以在我们的[文档页面](https://docs.phalcon.io/4.0/en/installation)中找到。

> 注意:从这篇博文发表时开始，DEB 和 RPM 包的构建可能需要一点时间。

### 谢谢

再次衷心感谢我们所有的贡献者！你们帮了我们很多。您可以通过安装和测试这个版本来帮助我们。如果您发现了 bug，请在我们的 [Github 问题](https://github.com/phalcon/cphalcon/issues)页面报告它们。或者，你可以随时加入我们的[不和谐服务器](https://phalcon.link/discord)或[论坛](https://phalcon.link/forum)。

最后，不要忘记在 [GitHub](https://phalcon.link/github) 上开始我们的项目，并在我们的社交媒体上关注我们:

聊天-问答

*   [不和谐聊天](https://phalcon.link/discord)
*   [论坛](https://phalcon.link/forum)

支持

*   [OpenCollective -支持我们](https://phalcon.link/fund)
*   [商店-商品](https://phalcon.link/store)

社会化媒体

*   [电报](https://phalcon.link/telegram)
*   [饶舌](https://phalcon.link/gab)
*   [MeWe](https://phalcon.link/mewe)
*   说话
*   [脸书](https://phalcon.link/fb)
*   [推特](https://phalcon.link/t)

录像

*   [BitChute](https://phalcon.link/bitchute)
*   [YouTube](https://phalcon.link/youtube)

< 3 Phalcon 团队