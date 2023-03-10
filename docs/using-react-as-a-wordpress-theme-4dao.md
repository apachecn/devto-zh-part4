# 使用 React 作为 Wordpress 主题

> 原文：<https://dev.to/technolaaji/using-react-as-a-wordpress-theme-4dao>

不久前，我成为了 wpBeirut (Wordpress Beirut)的联合组织者，这是一个致力于黎巴嫩贝鲁特 Wordpress 开发者的社区。

我从社区中意识到的一件事是，大多数人依赖插件在前端添加一些东西或添加功能，如站点构建器插件(Elementor 和 wpBakery)

有些人会去买一个预制好的主题并使用它，因为对他们来说，首先编写 php 代码没那么麻烦

在对社区做了一个小调查后，大多数人遵循这种模式有几个原因:

*   一个中等预算的小型自由项目，所以只需安装一些插件预制的主题
*   不知道如何写 php 代码(用另一种语言写)或者根本不知道如何编码(完全正常，因为 wordpress 可以被非技术人员使用)
*   喜欢用最新的技术(React/Angular/Vue)编写前端，不喜欢纯粹用 php 和一些 jquery 编写前端

即使是我自己，我也不喜欢用 php 编写前端，不是因为我讨厌 php 或任何东西，而是我觉得用 React 更舒服、更快

Gutenberg(一个 wordpress 插件，使用 React 使 wordpress 的前端变得更现代，更容易使用)对我来说不划算

即使你去了古腾堡并且创建了几个区块，你仍然会被 wordpress 的前端所困

# 所以是时候把 wordpress 当成一个无头 CMS 了

当我们谈论使用 wordpress 作为一个无头 CMS 时，理想情况下我们忽略了 wordpress 的前端，只依赖于 wordpress 支持的 REST api

wordpress 最酷的部分是编写执行 REST 动作的函数非常简单

这里有一个通过发送旧的和新的电子邮件地址来改变一个人的电子邮件地址的例子

```
add_action('rest_api_init', function () {
  register_rest_route( 'changemyemailman/v1', 'changedatmail',array(
                'methods'  => 'POST',
                'callback' => 'changeme'
      ));
});

function changeme (WP_REST_Request $request) {
    $oldmail = $request['old_mail'];
    $user = get_user_by( 'email', $oldmail );
$userId = $user->ID;
    $args = array(
    'ID'         => $userId,
    'user_email' => $request['new_mail']
);
wp_update_user( $args );
} 
```

只需发送一个 api 调用到/changemyemailman/v1/changedatemail，其中正文包含:

```
{
   "old_email": "john@doe.com",
   "new_email": "john1@doe.com"
} 
```

(我举这个例子是为了展示在 wordpress 上创建一个端点是多么容易，但是上面的例子绝对是一个安全噩梦，因为如果你知道原始的电子邮件和端点，你可以改变任何人的电子邮件地址，这样你就可以很容易地把管理员的电子邮件改成你的，并完全控制网站)

# 向 CORS 问好！

当你开始使用 wordpress 作为一个无头 CMS 时，你会开始看到这个街区的新成员

他们叫他 CORS，有时他可能是个顽童，但大多数时候他都在尽力帮忙

简而言之:CORS(跨来源资源共享)主要用于当您请求的数据不是来自同一个来源时

在 CORS 标准化之前，出于安全原因，无法在不同的域下调用 API 端点。这曾经(在某种程度上现在仍然)被同源政策所阻止。

你可以在这里了解更多信息[https://www.codecademy.com/articles/what-is-cors](https://www.codecademy.com/articles/what-is-cors)

# 再说说 CORS？🤔

CORS 是一种机制，旨在允许代表您发出的请求，同时阻止流氓 JS 发出的一些请求，并且在您向以下对象发出 HTTP 请求时触发:

*   一个不同的域(例如，位于 example.com 的站点称为 api.com)
*   一个不同的子域(如在 example.com 的网站称为 api.example.com)
*   不同的港口(如位于 example.com 的港口称为 example.com:3001)
*   不同的协议(例如位于 https://example.com[的站点](https://example.com)呼叫 http://example.com 的

这种机制可以防止攻击者在各种网站上植入脚本(例如，在通过谷歌广告显示的广告中)，对[www.yourbank.com](http://www.yourbank.com)进行 AJAX 调用，以防你使用*的*凭证登录进行交易。

如果服务器没有用特定的头来响应“简单的”GET 或 POST 请求，它仍然会发送，仍然会接收数据，但是浏览器不允许 JavaScript 访问响应。

如果您的浏览器尝试发出“非简单”请求(例如，包含 cookies 的请求，或者内容类型不是 application/x-ww-form-urlencoded、multipart/form-data 或 text-plain 的请求)，将会使用一种称为 preflight 的机制，并且会向服务器发送一个 OPTIONS 请求。

“非简单”请求的一个常见示例是添加 cookies 或自定义标题—如果您的浏览器发送了这样的请求，而服务器没有正确响应，则只会进行预检调用(没有额外的标题)，而不会发送浏览器想要发出的实际 HTTP 请求。

# 解决方法:使用与 Wordpress 相同原点的 React！🎉

每当我和任何一个 wordpress 开发者谈论这个话题时，他/她都会面临理解这个特定部分的困难

怎么能在和 wordpress 一样的原点使用 React？🤔

简单的部分是去掉默认的 wordpress 前端，使用 React，对吗？

对！但困难的是:我们如何才能做到这一点？🤔

嗯，有人非常好，创建了一个 create-react-app 风格的 cli 工具，将 react 用作 wordpress 主题

[https://www.npmjs.com/package/create-react-wptheme](https://www.npmjs.com/package/create-react-wptheme)

它做到了它所说的:使用 React 作为 wordpress 的主题，同时给你“创建-反应-应用”的感觉

# 爽！但是怎么用呢？🤔

非常简单，进入你的主题文件夹(在你的 wp-content 文件夹中)并在你的终端中输入这个命令

```
npx create-react-wptheme [name of your project] 
```

类似于 CRA 安装包，但有一个小的设置你应该先做

如果你进入 wordpress 的主题部分，你会发现它被标记为损坏和丢失的 css 文件

[![missing-css](img/8d015a9174c2d35451096175c15ef7bd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iKRlZrNf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dff7fjl6ttku1hepeasi.png)

在您的终端中，输入项目(我的项目名为 react-test ),然后进入 react-src 文件夹

然后写:

```
yarn start 
```

您将在终端上收到这条消息

[![Alt Text](img/a0d2a7a6eb1f404f099b6406bf509d6d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4KWjturx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k1mtgmndy1a5kdbag8ol.png)

所以去你的主题部分，你会看到你的反应主题

[![Alt Text](img/cbbfed5e407e49f5ced8d438bf949998.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TXWIiXhG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x7e5it2prk9dehvwe2z5.png)

点击实时预览，你会看到一个“立即重启节点观察器”的消息

[![Alt Text](img/dc4a4c631130efcc10278d32ce0f42a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xrmypCEQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hgfr1ghqgfs2xbh1ekzd.png)

启动服务器就行了！

```
yarn start 
```

[![Alt Text](img/694b40e867bb52613900e1008ea52619.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--baL_Oz_E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9jij0eil2xfrhnk0cvqq.png)

tada 🎉

你的终端会有一个实时的重新加载器，所以你做的任何改变，都会像创建-反应-应用程序一样刷新给观察者。

[![Alt Text](img/8fb12e9651e702e3b95a1bc4e5535c7f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zjRSWcRj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kdwzaciaid28owlyfvg9.png)

按下“激活并发布”按钮，它将成为你的 wordpress 站点的主题

[![Alt Text](img/59992f0af03447620d9bf8d7450feefe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RVQP7uM4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3r64tzqsi7tvz354cvca.png)

都在 8888 端口下！最酷的是，你可以使用 wordpress 提供的 REST api，而不会被 CORS 阻止！

所以如果你已经有了 wordpress 网站，但是你需要刷新前端，试试吧！

或者如果你知道 React 并且想使用 wordpress 这样的 CMS，那么天空就是极限！

有趣的事实:你可以使用任何你选择的 React 库，就像你正在使用 CRA，但它是在 wordpress 上

我在这种设置中所做的是:创建自定义帖子类型并在其上放置自定义字段，然后调用这些自定义帖子并在 React 应用程序中显示来自自定义字段的数据！

希望你喜欢这篇文章！请在评论区告诉我你的想法