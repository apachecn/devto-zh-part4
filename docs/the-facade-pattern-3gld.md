# 立面图案

> 原文：<https://dev.to/jackmarchant/the-facade-pattern-3gld>

设计模式允许您创建抽象，将代码库的各个部分解耦，目的是使以后对代码的更改变得更加容易。它们是解决特定问题的蓝图，希望不要过于复杂。

没有什么比在代码库中看到抽象比没有抽象更难理解的了。当然，这是一种权衡，但通常一种简单的方法是，当你开始在代码中看到行为的模式或重复时，你就应该创建一个抽象——不一定只是重复的代码。

我最近一直在钻研一些设计模式，我不得不再次研究的一个是 Facade 模式。如果你不知道它是什么，你可能已经见过或使用过很多次了，但是读完这篇文章后，希望你能在自己的代码中识别出 Facade 模式。

Facade 字面意思是欺骗性的外观，这可能是用软件解决问题的错误角度。当你创建一个新函数时，除了该函数的具体功能之外，你不太可能给它起其他名字。给事物命名本身就很难，但这至少应该是目标。

您的代码中使用的 Facade 模式应该是一个简单的接口，用于处理更复杂的事情。它应该把相关的东西组合在一起，以便于使用。

如果你曾经将第三方库集成到你的应用程序中，你可能下意识地使用了这种模式而没有意识到。假设你正在开发一个用户可以购物的应用程序，你可能想要创建一个新的客户帐户，用客户的信用卡付款，并发送一封发票电子邮件。

每当客户进行购买时，我们不必考虑这些需求中的每一个，我们可以将该功能包装在为进行购买而创建的特定类中，然后应用程序中的内部对象的构造是集中的和一致的，而不管购买的类型。这种类型的抽象将流程中一些复杂的部分隐藏在一个友好的界面后面，这个界面可以相对容易地在整个应用程序中使用。
就是这个接口，可以说是门面。

```
class Customer {
  public function __construct(array $details) {}
}
class PaymentService implements PaymentGateway {
  public function createCustomer(Customer $customer) {}
  public function createCharge(Customer $customer) {}
}
class Mailer {
  public static function send(string $to) {}
}

class PaymentFacade 
{
   public static function purchase(array $customerDetails, Item $item)
   {
      $customer = new Customer($customerDetails);
      $service = new PaymentService;
      $result = $service->createCustomer($customer)->createCharge($customer, $item->price);

      if ($result) {
        Mailer::send($customer->email);
      }

      return $customer;
   }
} 
```

对我来说，Facade 模式有点令人困惑，所以我花了一些时间来弄清楚为什么以及什么时候使用它。为了真正帮助学习软件中的设计模式，我建议阅读流行的项目源代码，这样你可以看到某些模式是如何使用的——然后你就能够在你自己的代码中识别它。

**原载于[jackmarchant.com](https://www.jackmarchant.com)T3】**