# Wordpress 中间件

> 原文：<https://dev.to/christopherarter/middleware-for-wordpress-g4k>

当我最初开始围绕 Wordpress REST API 进行重大开发时，我很兴奋终于可以亲自动手了。

然后，就来写“中间件”了。如果您不熟悉 web 开发中的中间件，它只是入站请求和控制器动作之间的一系列检查。

这是您可以根据当前 WP 代码执行中间件检查的方式:

```
<?php
add_action( 'rest_api_init', function () {
  register_rest_route( 'myplugin/v1', '/author/(?P<id>\d+)', array(
    'methods' => 'GET',
    'callback' => 'my_awesome_func',
    'args' => array(
      'id' => array(
        'validate_callback' => 'is_numeric'
      ),
    ),
    'permission_callback' => function () {
      return current_user_can( 'edit_others_posts' );
    }
  ) );
} ); 
```

我们剩下的就是`permission_callback`传入一个函数字符串，或者文档中看到的闭包。

我决定钻研源代码，看看能否创建一个更熟悉的中间件。

### WP 中间件

这时我创建了一个`middleware()`助手。

这个助手简单地从`rest_pre_dispatch`过滤器中取出`WP_REST_Request`，并将其作为参数注入到`rest_pre_dispatch`钩子的回调中。

这允许您编写简单的检查，将您需要的所有对象都放在一个地方:

```
 middleware()->get('/wp/v2/posts', ['check_foo', 'check_bar']);

// callback check
function check_foo($request) {
    if( $request->get_param('foo') != 'foo' ){
        return reject();
    }
}

// callback check
function check_bar($request) {
    if( $request->get_param('bar') != 'bar' ){
        return reject();
    }
} 
```

只需返回`reject()`函数来拒绝请求并返回一个`401`。您还可以传递自定义拒绝消息和响应代码。

```
return reject("foo doesn't equal bar!", 400); 
```

### 授权

这也为基于外部资源的特定 API 操作打开了大门，例如，使用来自 Cognito 的用户令牌，或者基于请求中的其他令牌数据(如 Stripe)批准或拒绝请求。

```
middleware()->post('/wp/v2/stripe-webhook', ['stripe_check']);

function stripe_check($request){
    // verify the webhook came from stripe
} 
```

### 中间件堆栈

这也允许您创建预先描述的中间件堆栈，允许跨应用程序的可移植的、统一的检查逻辑。

```
$middlewareStack = ['check_foo', 'check_bar'];

middleware()->guard([
    '/wp/v2/posts',
    '/wp/v2/users' ], 
    $middlewareStack); 
```

### 入门

有关文档，请参见[自述文件](https://github.com/christopherarter/WP-Middleware-Plugin/blob/master/readme.md)。

要将这个中间件作为插件安装，使用 [GitHub](https://github.com/christopherarter/WP-Middleware-Plugin)

通过 composer 安装:
`composer require christopherarter/wp-middleware`