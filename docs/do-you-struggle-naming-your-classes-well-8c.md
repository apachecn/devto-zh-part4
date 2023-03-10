# 你是否在努力为你的类取个好名字？

> 原文：<https://dev.to/jamesmh/do-you-struggle-naming-your-classes-well-8c>

有没有发现自己挠头的时候，不知道你应该给这个新的类命名？

我们都在为此奋斗！

事实上，[这是编程最难的两个部分之一！](https://www.martinfowler.com/bliki/TwoHardThings.html)

有时候这很容易，因为你头脑中有一个清晰的商业概念。

其他时候，您可能会创建与业务概念没有必然联系的类。

您可能决定将一个类分成几个类(为了可维护性)。

或者，您可能需要使用设计模式。

〈希莫丝〉

# 救援提示！

编写代码通常涉及到一些你试图解决的特定场景。这里有一些通过给你的类起个好名字来处理这种业务场景的技巧:

1.  尽可能具体！
2.  一开始不要怕啰嗦。您可以稍后重构。
3.  试着假装你正在为一篇向非程序员解释你的业务流程的文章写提纲。从用这些术语给事物命名开始。

## 举例

下面是使用技巧 3 的一个例子:

> 一旦客户提交订单，我们需要确保订单的所有商品都有库存。如果有些商品没有库存，我们需要给他们发一封电子邮件，让他们知道哪些商品缺货。
> 
> 接下来，我们会将信息传递给运输部门进行进一步处理。

我们可以使用这个描述从名词中创建一些类。有时，根据你想如何组织你的代码，使用形容词也是非常值得的！

*   `Order`或`SubmittedOrder`
*   `Customer`或`OrderCustomer`
*   `OrderItems`
*   `OrderItemsOnBackOrderEmail`
*   `ShippingDepartmentOrderHandler`

记住，名词/术语都适用于你试图解决的特定语境(参见 DDD 的[有界语境)。](https://www.martinfowler.com/bliki/BoundedContext.html)

我们编写的代码第一次看起来可能是这样的:

```
class OrderSubmittedHandler {

    public async handle(order: Order, customer: Customer) {
        const submitted: SubmittedOrder = order.submitOrder();

        if(submitted.hasItemsOnBackOrder()) {
            const mail = new OrderItemsOnBackOrder(submitted);
            mail.sendTo(customer);
            await this._mailer.send(mail);
        }

        const shipping = new ShippingDepartmentOrderHandler(submitted);
        await shipping.sendOrderInfo();
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

兴趣点:

*   注意，我们显式地对从`Order`到`SubmittedOrder`的转换建模。由于一只`SubmittedOrder`和一只`Order`有不同的行为，通过遵循[单一责任原则](https://www.weeklydevtips.com/episodes/049)，这将使你的班级更小更易管理。

*   我们引入了横切对象，如`_mailer`变量。在这个例子中，我已经知道我将使用依赖注入来提供`Mailer`。但是这可能是你以后决定重构的东西。

*   整个场景本身被一个有意义的名词捕获为一个新类！当然，这是可以事后重构的。但是作为第一步，这种技术确实有助于巩固事物的命名以及它们应该如何交互。

*   啰嗦实际上效果很好！

要了解更多关于使用伪代码的技巧，请查看由 [@aspittel](https://dev.to/aspittel) 撰写的这篇精彩文章:

[![aspittel](img/45a8d86fe0ff981bf7cdb45b471a3c5c.png)](/aspittel) [## 移动过去教程:伪代码

### 阿里·斯皮特尔 5 月 7 日 1915 分钟阅读

#beginners #problemsolving #movingpasttutorials #programming](/aspittel/moving-past-tutorials-pseudocode-13a6)

# 多一些指引

这里有一些指导方针，在处理特定种类的类时可能会对您有所帮助，这些类在业务场景中可能很明显，也可能不明显。

例如，有时你需要引入一种设计模式。那你给你的班级取什么名字？

| 目的 | 公式 | 例子 |
| --- | --- | --- |
| 批准 | `Can{Entity}{Action}` | `CanAdminViewThisPage`，`CanManagerUpdateUserInfo` |
| 确认 | `Is{Target}{State}{Test}` | `IsAddressUpdateAllowed`，`IsUserCreationValid` |
| 接口 | `ICan{Action}` | `ICanSendMail`，`ICanBeValidated` |
| 具体的商业概念 | *“什么事？”*(名词+形容词) | `Student`、`EmployeeUserProfile`、`ShippingAddress` |
| 用例 | `{Action}{Target}` | `ApproveOrder`，`SendWelcomeEmail` |
| 设计模式 | `{Name}{Pattern}` | `IShippingAddressStrategy`、`HomeAddressStrategy`、`TemporaryAddressStrategy` |

# 保持联系

不要忘记通过以下方式与我联系:

*   [推特](https://twitter.com/jamesmh_dev)
*   [LinkedIn](https://www.linkedin.com/in/jamesmhickey/)

你也可以在我的网站[www.jamesmichaelhickey.com](https://www.jamesmichaelhickey.com)找到我。

# 浏览您的软件开发职业简讯

一封电子邮件简讯，将帮助您提升软件开发人员的职业水平！有没有想过:

✔:软件开发人员的一般阶段是什么？✔:我怎么知道自己处于哪个阶段？我如何进入下一阶段？
✔什么是技术领导者，我如何成为一名技术领导者？✔，有人愿意和我一起走走，回答我的问题吗？

听起来有趣吗？加入社区吧！