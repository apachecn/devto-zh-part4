# Java 中的不可变数据结构

> 原文：<https://dev.to/bodiam/immutable-data-structures-in-java-34a1>

作为我最近进行的一些编码访谈的一部分，不可变数据结构的主题经常出现。我自己并不太武断，但是每当不需要可变状态的时候，我都试图去掉那些使代码可变的代码，这在数据结构中最明显。然而，似乎对不变性的概念有一点误解，开发人员通常认为拥有一个`final`引用，或者 Kotlin 或 Scala 中的`val`，就足以使一个对象不可变。这篇博文更深入地探讨了不可变引用和不可变数据结构。

**不可变数据结构的好处**

不可变数据结构有一些显著的好处，例如:

*   没有无效状态
*   线程安全
*   更容易理解代码
*   更容易测试
*   可用于值类型

**无无效状态**

当一个对象不可变时，很难让对象处于无效状态。该对象只能通过其构造函数进行实例化，这将强制对象的有效性。通过这种方式，可以强制实施有效状态所需的参数。
一个例子

```
Address address = new Address();
address.setCity("Sydney");
// address is in invalid state now, since the country hasn’t been set.

Address address = new Address("Sydney", "Australia");
// Address is valid and doesn’t have setters, so the address object is always valid.
```

**线程安全**

因为对象不能被改变，所以它可以在线程之间共享，而没有竞争条件或数据突变问题。

**更容易理解的代码**

类似于无效状态下的代码示例，使用构造函数通常比使用初始化方法更容易。这是因为构造函数强制执行所需的参数，而 setter 或 initialiser 方法在编译时不强制执行。

**更容易测试**

因为对象更容易预测，所以没有必要测试初始化方法的所有排列；即当调用一个类的构造函数时，对象要么有效，要么无效。使用这些类的代码的其他部分变得更加可预测，出现 NullPointerExceptions 的机会更少。有时，在传递对象时，有些方法可能会改变对象的状态。例如:

```
public boolean isOverseas(Address address) {
    if(address.getCountry().equals("Australia") == false) {
        address.setOverseas(true); // address has now been mutated!
        return true;
    } else {
        return false;
    }
}
```

上面的代码，总的来说，是不好的做法。它返回一个布尔值，并可能改变对象的状态。这使得代码更难理解和测试。更好的解决方案是从 Address 类中删除 setter，并通过测试国家名称返回一个布尔值。更好的方法是将这个逻辑移到 Address 类本身(Address . iso overseas())。当确实需要设置状态时，在不改变输入的情况下复制原始对象。

**可用于值类型**

想象一笔钱，比如 10 美元。10 美元永远是 10 美元。在代码中，这可能看起来像公共资金(最终 BigInteger 金额，最终货币货币)。正如您在这段代码中看到的，不可能将 10 美元的值更改为除此之外的任何值，因此上述内容可以安全地用于值类型。

**最终引用不会使对象不可变**

如前所述，我经常遇到的一个问题是，大部分开发人员并不完全理解最终引用和不可变对象之间的区别。似乎这些开发者的共同理解是，变量成为 final 的那一刻，数据结构就变成了不可变的。可惜事情没那么简单，我想把这个误会一劳永逸地赶出这个世界:

最终引用不会使你的对象不可变！

换句话说，下面的代码没有**而不是**让你的对象不可变:

```
final Person person = new Person("John");
```

为什么不呢？嗯，虽然“person”是一个最终字段，不能被重新分配，Person 类可能有一个 setter 方法或其他 mutator 方法，产生类似于:
的动作

```
person.setName("Cindy");
```

不管最后的修饰词是什么，这都是很容易做到的事情。或者，Person 类可以公开一个地址列表，如下所示。访问这个列表允许您添加一个地址，因此像这样改变 person 对象:

```
person.getAddresses().add(new Address("Sydney"));
```

我们的最后一个引用也没有帮助我们阻止 person 对象的变异。

好了，现在我们已经解决了这个问题，让我们稍微深入一下如何让一个类成为不可变的。在设计我们的类时，有几件事我们需要记住:

*   不要以可变的方式公开内部状态
*   不要在内部改变状态
*   确保子类不会覆盖上述行为

有了下面的指导方针，让我们设计一个更好的 Person 类版本。

```
public final class Person {  // final class, can’t be overridden by subclasses
    private final String name;     // final for safe publication in multithreaded applications
    private final List<Address> addresses;

    public Person(String name, List<Address> addresses) {
        this.name = name;
        this.addresses = List.copyOf(addresses);   // makes a copy of the list to protect from outside mutations (Java 10+). 
                // Otherwise, use Collections.unmodifiableList(new ArrayList<>(addresses));

    }

    public String getName() {
        return this.name;   // String is immutable, okay to expose
    }

    public List<Address> getAddresses() {
        return addresses; // Address list is immutable
    }
}

public final class Address {    // final class, can’t be overridden by subclasses
    private final String city;          // only immutable classes
    private final String country;

    public Address(String city, String country) {
        this.city = city;
        this.country = country;
    }

    public String getCity() {
        return city;
    }

    public String getCountry() {
        return country;
    }
}
```

现在，下面的代码可以这样使用:

```
import java.util.List;
final Person person = new Person("John", List.of(new Address(“Sydney”, "Australia"));
```

现在，由于 person 和 Address 类的设计，上面的代码是不可变的，同时还具有最终引用，这使得不可能将 Person 变量重新分配给任何其他变量。

**更新:** *正如[一些人](https://www.reddit.com/r/java/comments/azryu6/final_vs_immutable_data_structures_in_java/?st=jt74o32w&sh=40d418d3)提到的，上面的代码仍然是可变的，因为我没有在构造函数中复制地址列表。因此，不在构造函数中调用新的 ArrayList()，仍然可以执行以下操作:*

```
final List<Address> addresses = new ArrayList<>();
addresses.add(new Address("Sydney", "Australia"));
final Person person = new Person("John", addressList);
addresses.clear();
```

然而，由于现在在构造函数中创建了一个副本，上面的代码将不再影响 Person 类中被复制的地址列表引用，从而使代码变得安全。感谢所有发现！

我希望以上有助于理解最终和不变性之间的区别。如果你有任何意见或反馈，请在下面的评论中告诉我。

再次感谢我的同事 Winston 花时间校对和审阅这篇博文！