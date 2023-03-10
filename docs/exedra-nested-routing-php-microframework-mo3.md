# 嵌套路由 PHP 微框架

> 原文：<https://dev.to/eimihar/exedra-nested-routing-php-microframework-mo3>

## 简介

一个 PHP 微框架，专注于嵌套/分组路由，同时能够根据上下文附加一个中间件。嵌套级别是无限的。

想象有这样的 uri:

```
GET    /apis/users
POST   /apis/users
GET    /apis/users/:user-id
PATCH  /apis/users/:user-id
GET    /apis/users/:user-id/friends
DELETE /apis/users/:user-id/friends/:friend-id 
```

在这种情况下，您应该做类似于
的事情

```
use Exedra\Routing\Group;
use Exedra\Runtime\Context;

require_once __DIR__ .'/vendor/autoload.php';

$app = new \Exedra\Application(__DIR__);

$app->map['apis']->any('/apis')->group(function(Group $apis) {
  $apis['users']->any('/users')->group(function(Group $users) {
    $users['list']->get('/')->execute(function(Context $context){});
    $users['add']->post('/')->execute(function(){});
    $users['user']->any('/:user-id')->group(function(Group $user) {
      $user['get']->get('/')->execute(function(){});
        $user['update']->patch('/')->execute(function(){});
        $user['friends']->group(function(Group $friends) {
          $friends['list']->get('/')->execute(function(){});
          $friends['delete']->delete('/:friend-id')->execute(function(){});
        });
      });
  });
}); 
```

运行 symfony 控制台命令会得到类似于
的结果

```
Showing list of routes :
+--------------------------------+--------+-----+---------------------------------+
| name                           | method | tag | uri                             |
+--------------------------------+--------+-----+---------------------------------+
| apis.users.list                | get    |     | /apis/users                     |
| apis.users.add                 | post   |     | /apis/users                     |
| apis.users.user.get            | get    |     | /apis/users/:user-id            |
| apis.users.user.update         | patch  |     | /apis/users/:user-id            |
| apis.users.user.friends.list   | get    |     | /apis/users/:user-id            |
| apis.users.user.friends.delete | delete |     | /apis/users/:user-id/:friend-id |
+--------------------------------+--------+-----+---------------------------------+ 
```

随着深度的增加，原始样本可能看起来很复杂。在这种情况下，您可能希望使用基于类的路由控制器作为组模式。[相关单据](http://exedra.rosengate.com/docs/controller/introduction)

## 文档

如果有一天这个网站倒闭了，那就去 http://exedra.rosengate.com 或者 http://github.com/rosengate/exedra-web[吧](http://github.com/rosengate/exedra-web)

## 为什么是另一个框架

我只需要一个能做嵌套路由的路由器。所以，我在 2016 年写了一个，但还没有真正发布为 1.0.0(现在是 0.9.4)，也没有真正向公众发布。以前在一些个人项目中使用过，但是已经 4 年了，我想我只是想永远完成它。因为它是一个微框架，它不需要太多的组件，因为还有很多其他优秀的包，更不用说 Symfony 包了。

它目前拥有的基本组件是 php 会话、基于注释的路由控制器、路径、url 工厂和其他一些我可能已经忘记的东西。

## 为什么要嵌套路由

主要是分离关注点。想象一下，你的应用程序有 200 条路径，所有事情都发生在一个平面注册表中，维护它们将是一场噩梦。拥有专用路由组的另一个好处是，您可以匿名地为一个组附加某种中间件(或身份验证层)。

其次，很多框架/微框架都明确地将它们的路由映射到动作/控制器，对我来说，随着路由变得越来越大，分别管理路由和控制器/动作类会带来认知负担。因此，我构建了一个基于注释的路由控制器，专用于在同一个地方处理路由和动作。

## 谢谢

我计划在完成一些路由控制器的单元测试后，将它发布为 1.0.0 版本。

欢迎任何反馈。谢谢你。