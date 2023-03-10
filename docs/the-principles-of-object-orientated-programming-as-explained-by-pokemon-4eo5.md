# 神奇宝贝解释的面向对象编程原则

> 原文：<https://dev.to/iamtravisw/the-principles-of-object-orientated-programming-as-explained-by-pokemon-4eo5>

[![The Principles of Object Orientated Programming as explained by Pokémon](img/1ac51bd7918db884087051e189ef6e0a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rBzFF9JJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://iamtravisw.com/conteimg/2019/08/pexels-photo-1310847.jpeg)

为什么是神奇宝贝？为什么不呢？我的许多同龄人伴随着神奇宝贝长大，或者现在有了看神奇宝贝的孩子。此外，许多初学者很难掌握面向对象编程的基础。我的希望是，将这四个核心原则与许多人已经看到的东西结合起来，将有助于它的成功。通过让这个话题不那么抽象(这很讽刺)，更有关联，它可以帮助人们掌握基本知识。我们还能要求什么呢？所以让我们开始吧！

*注:如果你不关注 Pok* é *mon，这篇文章可能没有多大意义。*

[![The Principles of Object Orientated Programming as explained by Pokémon](img/2d23a87b75f3b12ddb79af581bde97ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--T49O_XCX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://iamtravisw.com/conteimg/2019/08/chancey.gif)

### 什么是面向对象编程？

第一；面向对象编程(OOP)是一种编程语言模型，在这种模型中，对象相互通信以解决问题。对象是可以重用的代码片段，因此您不必总是重写它。对象在类中使用，而类只是这些对象的蓝图。通过使用这些蓝图(或类),您可以创建这些对象的实例，它们本质上只是原始对象的版本，从蓝图中构建。

好吧，这是一个小段落中的很多内容，但在继续之前，理解这一点很重要。这也是整篇文章中唯一不会使用神奇宝贝来帮助你学习的部分，所以确保你在阅读 rest 之前掌握了这些概念。*对象*在类中使用。*级*是蓝图。*对象的实例*就是从这些类中构建的。

### 封装

[![The Principles of Object Orientated Programming as explained by Pokémon](img/5a5a97a549de0837a31037d0daf542c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aNGe3Ky7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://iamtravisw.com/conteimg/2019/08/pokeball.gif)

封装是将函数和数据组合成一个类的过程。数据不是直接访问的，而是通过类中的函数访问的。封装的目的是限制对对象组件的访问，这样用户就不会直接修改对象。

把封装想象成一个神奇的球。每个人都知道如何使用它，不管它在内部如何工作。口袋妖怪训练师不需要知道它为什么会起作用，他们只需要知道如何扔它，并按规则玩。在编程中，除了你提供的接口之外，用户不需要知道任何东西，你只想给他们提供访问相关信息的途径。这就是所谓的信息隐藏，这很重要。

为了正确使用封装，您需要使用访问修饰符。不同的语言使用不同的访问修饰符，但是在 Java 中它们是:

**Public:** 应用程序中任何地方的代码都可以访问它。

**私有:**只有同类内的代码才能访问。

**默认:**当你没有为你的类定义一个访问修饰符时，你将使用默认的访问修饰符。同一类和包中的代码可以访问这些代码。

**Protected:**Protected 类具有与默认相同的访问权限，但增加了能够访问代码的子类。

### 继承

[![The Principles of Object Orientated Programming as explained by Pokémon](img/4b79477948729a17a4c99e4c41355804.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K1VkGBnQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://iamtravisw.com/conteimg/2019/08/char.gif)

编程中的继承是指一个类从另一个类获取属性。在 Java 中，一个类只能从另一个类派生。我们称原始类为父类(或超类)。派生类称为子类(或子类)。这是神奇宝贝作为对比真正开始发光的地方。还记得进化论吗？记得 Charamader 变成 Charmeleon 的时候。这实际上是遗传的一个很好的例子。

假设你有一个 Charmader(超类),它知道两个基本动作。擦伤和烧伤。突然你的小火龙强大到足以进化，它变成了 Charmeleon(子类)。Charmeleon 学习了一个叫做火焰喷射器的新招式，但是它仍然可以使用抓和烧，因为它继承了小火龙(超职业)的这些能力。哇，这有点道理吧？

这是面向对象编程中继承的基本概念。如果你正在写一个程序，你不会想一遍又一遍地写同样的代码，那是没有效率的，管理起来也是一场噩梦。相反，您可以用一个新的子类扩展您的父类，并且父类的所有原始属性都将扩展到子类。同样的结果，更有效率，但工作量少了很多。

### 多态性

[![The Principles of Object Orientated Programming as explained by Pokémon](img/5d8763259435666ed91a2d098fa6bbb3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TiHT0rc9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://iamtravisw.com/conteimg/2019/08/eevee.gif)

多态性是采取多种形式的能力。在编程中，多态性意味着根据对象的数据类型不同地处理对象。换句话说，如果您有一个方法，那么它可能有某个类操作的多个实现。在应用程序运行时，它将决定做什么。

在《神奇宝贝》中，有两个角色突出显示了这种能力，同上和 Eevee。让我们以 Eevee 为例。如果你有一个名为 Eevee 的父类，它有多个子类(水伊布、利弗昂、雷伊布等...)，然后让 Eevee 接触一块进化石...相应的子类将被调用。因此，如果 Eevee 接触到一块水石，水伊布子类将在运行时被触发，而水伊布代码将被用于反对 Leafeon，雷伊布等...

那么如何使用多态性呢？在 Java 中，多态性可以是静态的，也可以是动态的。方法重载是静态的，方法重写是动态的。**重载**被认为是静态的，因为调用的方法是在编译时决定的。当共享*相同名称*的方法基于传递给方法的参数表现不同时，就使用了重载。下面是一个代码示例:

```
public class Eevee {

    public void sound () {
        System.out.println("Eevee");
    }

    public void sound (int num) {
        for (int n : num) a
        {  
            System.out.println("Eevee");    
        }    
    }
} 
```

**覆盖**被认为是动态的，因为它在运行时被解析。重写意味着派生类正在实现其父类的方法。如果这听起来令人困惑，那就想想我们讨论过的 eeve 解决方案的例子。以下是代码形式:

```
class Eevee{
    public void sound(){
        System.out.println("Eevee");
    }
}
class Vaporeon extends Eevee{
    public void sound(){
        System.out.println("Vaporeon");
    }
}

public class OverridingTest{
    public static void main(String [] args){
        Eevee eevee = new Vaporeon();
        eevee.Vaporeon();
    }
} 
```

在本例中，“水伊布”将是输出。

### 抽象

[![The Principles of Object Orientated Programming as explained by Pokémon](img/5f3067ca4dbbfaef1495ea8e0ce56c1d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ni_GL9IT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://iamtravisw.com/conteimg/2019/08/trade.gif)

抽象是只向用户显示相关数据并隐藏不必要信息的过程。神奇宝贝的一个很好的例子是和朋友交易。在这个过程中，幕后肯定有很多事情在进行。大量的数据被传输，文件被更新，对象被实例化。然而对用户来说，你所看到的只是你的神奇球被机器吸走，在新的神奇球落到你面前之前消失了。这就是抽象。就是这样。没必要变得比这更复杂。

### 包起来

[![The Principles of Object Orientated Programming as explained by Pokémon](img/c96f68ea39e7f2956063963048215db7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SN4awGie--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://iamtravisw.com/conteimg/2019/08/pikachu.gif)

不言而喻，这是对面向对象编程的简化解释，但是我希望这篇文章能够帮助那些一直在努力理解这些概念的人。和大多数事情一样，编程是一项技能，需要大量的练习才能掌握。如果这些概念不完全有意义，你应该更深入地研究它们，因为它们是 OOP 的核心基础。拥有强大的基础是培养你技能的最好方法。我给你们大家留下必不可少的皮卡丘 GIF。感谢阅读。