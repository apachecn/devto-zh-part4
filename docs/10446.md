# 假人的单一责任原则

> 原文：<https://dev.to/skill_pathway/single-responsibility-principle-for-dummies-59gb>

# 传统解释

你不是傻瓜-原因如下。看一看这个简单易懂、简明扼要的 SRP 解释:

> *单一责任原则*是一条计算机编程原则，它规定每个模块、类或函数都应该对软件提供的功能的单一部分负责，并且这种责任应该完全由类来封装。它的所有服务都应严格符合这一职责。

这就是我们英国人的讽刺，这些抽象而冗长的解释对初学者来说没什么帮助。我们知道这一点，因为我们曾经是初学者，直到这些东西在上下文中被解释*，像这样的定义是没有用的。*

那是因为*语境*把*【抽象理解】*转化为*【具体理解】*

如果你不能用简单的术语解释，你知道你对某件事的理解是抽象的，所以让我们先用真实世界的术语解释，然后再看上下文编程的例子

# 现实世界中的单一责任原则

这是吉姆(*一会儿你就知道他为什么难过了* )
[![](img/fd163fc774e77423853c325b13c6116c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5OgjIbhu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/92vbydg6a0vmw0a5xh6n.png)

吉姆在销售高端笔记本电脑的 *Pear Inc.* 公司担任*的人力资源经理*。

这是乔治，他的老板。IDK，平面设计部门显然正在休假
[![](img/4c4461fc16a6170ea084f3953f05eceb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--auK-2JAx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gcr6if5o7pq72kq3hms6.png)

乔治感到沮丧，因为他需要去找许多不同的员工让他们做不同的事情，所以他给了*吉姆*更多的*职责*

吉姆的职责应该只是*处理与员工合同相关的事情，比如假期、入职和离职*

最重要的是，乔治希望他

*   管理企业的现金流
*   维护网站
*   午饭时间给大家做饭
*   给办公室的狗拍照，然后发到 instagram 上
*   后期制作公司的宣传视频

乔治违反了*单一责任原则*。吉姆不仅做太多的事情，而且没有专攻任何一项。

乔治应该做的是雇佣*一名摄像师、一名开发人员、一名厨师、一名社交媒体经理和一名会计*——这将遵守*单一责任原则*。

# 编程世界中的单一责任

很明显，任何把那么多责任强加给一个人力资源部的老板都没有多少脑细胞。但是在编程世界里，*单一责任并不完全明显*——如果你违反了它，你会被原谅。

让我们看两个例子，看看如何发现它。这是我们需要编写的一部分功能

*   当用户按下支付按钮时
*   我们需要从他们的篮子里的项目获得总价格支付
*   然后，我们需要获取他们的信用卡信息，并将其传递给支付提供商
*   然后，我们需要将用户重定向回支付提供商的页面

在这两个例子中，所有的事情都会发生在一个`ShopController` a 类中的`pay`方法中——除了一个会违反 SRP，另一个不会。*而且，它们被过度注释了，所以你不需要担心语言语法。只要了解它在做的所有不同的事情*

```
class ShopController {

  // This gets run when the presses “pay now”
  public function pay()
  {
    // Get the basket out of the session
    $basket = $_SESSION[‘basket’];

    // Get the user out of the session
    $user = $_SESSION[‘user’];

    // Initialise a variable that will hold the price to pay
    $totalToPay = 0;

    // Add up the price of all the items in the basket
    foreach ($basket[‘items’] as $item) {
      $totalToPay += $item[‘price’]         
    }

    // Get the credit card information the user sent through
    $creditCardInformation = $_POST[‘credit_card_info’];

    // Get the payment gateway key and post the customer, price and card data to stripe
    $paymentGatewayKey = ‘A82hDiha9hhdaonldtumpr2390Jf’;
    $paymentResponse = API::post(‘stripe’, [
      ‘credit_card’ => $creditCardInfo,
      ‘customer’ => $user,
      ‘price’ => $totalToPay
    ]);

    // If the payment worked, take them to the payment gateway to confirm
      if ($paymentResponse[‘success’]) {
      return redirect($paymentResponse[‘redirect_url’]);
    } else {
      // Otherwise, show an error
      throw new Exception(“There was an error when attempting to pay. - ” . $paymentResponse[‘error_message’]);
    }
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

```
class ShopController {

  // This runs when the user presses “pay now”
  public function pay()
  {
    // Create a basket to handle our session basket data
    $basket = new Basket($_SESSION[‘basket’]);

    // Get the current user
    $user = (new Authenticator)->currentUser();

    // Create a payment request that holds payment data
    $paymentRequest = new PaymentRequest($_POST);

    // Pay and redirect the user (this throws an exception if there are any issues)
    return $basket->payAndRedirect($user);
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

我们来拿分数——第一个例子，SRP 被严重违反。它负责:

*   从会话中获取用户
*   处理过帐数据
*   向支付网关发出请求
*   合计篮子的总费用
*   确保支付成功，否则抛出异常
*   如果成功，将用户重定向到支付网关
*   存储支付密钥(这是一个可怕的安全风险——你的支付密钥现在在你的 GitHub 存储库中。干得好，乔治！)

在第二个例子中，责任被卸载到*专家类*上，这些专家类只处理他们需要处理的事情。在第二个例子中，控制器只负责:

*   获取请求数据并将其传递给必要的专家

我们可以深入到`Basket`、`Authenticator`和`PaymentRequest`类，这些类甚至可能使用它们自己的专业类。这就是*单责原则*的极致！

# 那么，我怎么知道什么时候分离责任呢？

不经过一段时间的编码和提炼，不阅读有经验的开发人员的代码，这是很难学会的。这是一个感觉的事情-随着时间的推移，你会更好地理解，但这里有一个很好的捷径。

## 阅读主要开源库的代码(那些有大量评论的)

观察有经验的开发人员如何在他们的代码中进行分离是理解 SRP 的最快方法之一。还是那句话，因为它不是抽象的，而是具体的。这是在真实世界代码的上下文中。

# 还有其他你觉得难以理解的编程设计模式吗？

让我们在下面的讨论中知道，这是我们的“傻瓜编程原则”系列的第一篇文章，我们想让尽可能多的初学者能够接触到它们。因为它能让我们写出更好的代码！

# 好奇前端开发？我们刚刚发布了一个免费速成课程(不，它真的是免费的——没有升级，没有隐藏成本)

我们花了几个月的时间制作了一个*专业设计*和 *4 小时 20 分钟*的视频内容，为您带来我们的免费速成课程，向您展示如何编写这个投资组合网站(我们不断鼓励您根据自己的风格进行定制)

如果你对此感兴趣，并且想了解更多关于 HTML、CSS、SCSS、Bootstrap 4、Git、GitHub 页面以及一些 JavaScript 和 jQuery 的知识，你可以今天就通过这里的链接免费注册。