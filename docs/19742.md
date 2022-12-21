# 使用设计模式防止破窗理论

> 原文：<https://dev.to/derickmr/preventing-broken-window-theory-using-design-patterns-2pp8>

由安德鲁·亨特和戴维·托马斯在《实用程序员:从熟练工到大师》中介绍的破窗理论是软件开发中最重要的概念之一。作者认为，事物，比如一个系统，会遭受一种叫做熵的东西。熵作为一个物理术语，翻译过来就是系统中的无序程度。不幸的是，这个水平总是趋向于最大值，这意味着，如果有熵，熵就会越来越多。

1982 年，詹姆斯·q·威尔逊(James Q. Wilson)和乔治·l·凯灵(George L. Kelling)在他们的文章《破窗》中，举了如下例子:想象一座有一些(几个)破窗的建筑。如果窗户不打算被尽快修理，趋势是其他人(例如，破坏者)将打破更多的窗户，并且最终，闯入建筑物，在里面点燃篝火，等等。

但是，在 1969 年，斯坦福大学心理学教授菲利普·津巴多用一种实际的方法测试了这个理论。他在布朗克斯区留下了一辆没有牌照的汽车，在帕洛阿尔托又留下了一辆同样状况的汽车。位于布朗克斯的汽车在被遗弃后几分钟内就遭到了袭击。不到 24 小时后，破坏者就把车上所有值钱的东西都拿走了。过了一段时间，汽车的窗户被打碎了，室内装潢被撕破了，孩子们把汽车当成了游乐场。与此同时，帕洛阿尔托的汽车保持原样。在一周没有人对汽车做任何事情后，津巴多自己用大锤砸了它。此后不久，另一辆车也开始发生同样的事情。人们也开始破坏汽车。津巴多注意到，在这两种情况下，袭击汽车的人主要是衣着光鲜、看上去体面的人。

现在你可能会想:“好吧，我正在开发的软件的‘窗口’怎么会坏呢？”。嗯，类似于 Zimbardo 的经验，如果有一段质量差的代码，那么倾向于会有更多质量差的代码。记住，有熵，就有更多的熵。

所以，让我们想象一下:你是一个开发人员，你的任务是为你的系统开发一个功能，根据它的类型为用户计算税金。出于某种原因，您需要在几个小时内交付任务，所以您的最终代码看起来像这样:

```
public class User {

    private String userType;

    public void calculateTaxes() {
        if (userType.equals("A")) {
            //do A
        } else if (userType.equals("B")) {
            //do B
        } else if (userType.equals("C")) {
            //do C
        } else {
            //do D
        }
    }
} 
```

这段代码有用吗？是的，绝对的。这将是完美的罚款为您的必要性提供它在短时间内。但是，有一扇巨大的破窗户。它从干净的代码中强加了一系列规则:为什么用户类要计算自己的税金？用户不应该这样做。这是一个巨大的责任问题。第二个大问题是:如果，在另一个时刻，其他人想要计算 C 类用户的税款，开发人员会怎么做？可能会添加另一个 else if 条件，或者最好将代码改为 switch case。代码会越来越多。为什么呢？因为你启动了错误的代码。你打破了第一扇窗户，没有修好就扔在那里。有熵就有更多熵。

所以，考虑到你打破了窗户，但是你想尽快修好它，这样其他的窗户就不会被打破了。能做些什么？

可以注意到的第一个问题是用户类正在做不应该做的事情。考虑到用户是一个模型，可能存储在应用程序的数据库中，它不应该包含任何“高级”逻辑。让我们通过创建一个为用户服务的类来解决这个问题。

```
public class UserServiceImpl implements UserService {

    @Override
    public void calculateTaxesForUser(User user) {
        if (user.getUserType().equals("A")) {
            //do A
        } else if (user.getUserType().equals("B")) {
            //do B
        } else if (user.getUserType().equals("C")) {
            //do C
        } else {
            //do D
        }
    }

} 
```

问题都解决了吗？绝对不行。还有一个责任的问题。如果我们想计算另一个东西的税收，而不仅仅是用户，我们需要创建另一个类来完成这项工作。那么为什么不把所有的税收计算逻辑放在一个中央类中，只负责税收的计算呢？

```
public class TaxesCalculationServiceImpl implements TaxesCalculationService {

    @Override
    public void calculateTaxesForUser(User user) {
        if (user.getUserType().equals("A")) {
            //do A
        } else if (user.getUserType().equals("B")) {
            //do B
        } else if (user.getUserType().equals("C")) {
            //do C
        } else {
            //do D
        }
    }

} 
```

现在可以说解决了第一个问题:责任。用户类别不计算自己的税款。当然，如果我们想要操作结果，我们可以从用户服务中调用计算税款的方法，但是我们不能在那里进行计算。现在，有了这个改进，我们的代码变得更好，我们的窗口不再那么破碎了。现在，如果其他人想扩展税收计算方法的功能，至少在正确的地方会这样做。或者在一个没那么糟糕的地方。

但是，代码远非完美。仍然存在一个巨大的问题:代码支持添加另一个 if 语句，以增加用户的选择。这怎么解决？策略设计模式(或函数指针)有答案。

设计模式被认为有助于代码的开发。当然，有时它们可能不那么容易实现，但在大多数情况下，这是值得的。

策略设计模式建议如下:如果有一个类以各种不同的方式做特定的事情，那么这些方式应该被分成不同的类，它们被称为*策略*，有一个实现的公共接口。这正是我们的问题。进行计算的方法对于每种类型的用户以不同的方式进行计算。因此，解决方案是将这些方法分成不同的类:

```
public class TaxCalculationStrategyForUserTypeA implements TaxCalculationStrategy {

    @Override
    public void calculateTaxesForUser(User user) {
        //do A
    }
} 
```

```
public class TaxCalculationStrategyForUserTypeB implements TaxCalculationStrategy {

    @Override
    public void calculateTaxesForUser(User user) {
        //do B
    }
} 
```

```
public class TaxCalculationStrategyForUserTypeC implements TaxCalculationStrategy {

    @Override
    public void calculateTaxesForUser(User user) {
        //do C
    }
} 
```

```
public class TaxCalculationStrategyForUserTypeD implements TaxCalculationStrategy {

    @Override
    public void calculateTaxesForUser(User user) {
        //do D
    }
} 
```

现在我们已经有了针对这类用户的所有策略，服务看起来将是这样的:

```
 public class TaxesCalculationServiceImpl implements TaxesCalculationService {

    @Resource
    private TaxCalculationStrategy taxCalculationStrategy;

    @Override
    public void calculateTaxesForUser(User user) {
        taxCalculationStrategy.calculateTaxesForUser(user);
    }

    @Override
    public void setTaxCalculationStrategy(TaxCalculationStrategy taxCalculationStrategy) {
        this.taxCalculationStrategy = taxCalculationStrategy;
    }

} 
```

通过这种方式，可以在需要的时候改变服务在运行时使用的策略:

```
public class Application {

    @Resource
    private TaxesCalculationService taxesCalculationService;

    public void doTaxCalculationForUser (User user){
        taxesCalculationService.setTaxCalculationStrategy(new TaxCalculationStrategyForUserTypeC());
        taxesCalculationService.calculateTaxesForUser(user);
        taxesCalculationService.setTaxCalculationStrategy(new TaxCalculationStrategyForUserTypeB());
        taxesCalculationService.calculateTaxesForUser(user);
    }

} 
```

当然，每个应用程序都会以自己的方式实现它。

您可能会想:“多态性也可以解决这个问题”。是的，确实如此。也许不会是最好的选择，但早就解决了，的确。但是，关键是我们现在的代码更好了。我们已经修好了留下的破窗户。可能扩展功能的人会检查实现情况并遵循模式，保持代码整洁。

破碎的窗口应该是软件开发中经常关注的问题。拒绝自己在糟糕的代码上继续开发。首先修理破碎的窗户，寻找最好的方法，学习新的东西，防止新的东西出现。这不仅会使你在技术上成为一个更好的开发人员，而且会从整体上提高你的开发旅程的质量。

没有熵，就不会有熵。