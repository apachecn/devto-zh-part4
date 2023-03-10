# 多态性:灵丹妙药与 Java

> 原文：<https://dev.to/hugecoderguy/polymorphism-elixir-vs-java-14i0>

## 简介

本文试图通过将 Elixir 协议与 Java 中的类层次结构进行比较来加深对它们的理解。尽管这两种语言差别很大，但它们有一个共同的强大特性:多态性。

当你第一次开始学习 Elixir 时，人们说你必须做的第一件事就是思考函数式编程(FP)。这可能意味着摒弃使用传统的面向对象语言(如 Java 和 Python)形成的一些旧习惯，并且在编写代码和设计软件时稍微改变一下思维方式。

尽管每个 Elixir 开发者都会告诉你 FP 有多棒，为什么他们喜欢它，但是对于初学者来说，一个鲜为人知的特性是它通过协议支持多态性。

多态性是一个处于面向对象编程核心的概念，这使得它成为一个似乎与 Elixir 语言的核心相反的特性。我们将了解它为什么有用，并将它们与大家熟悉的 Java 基本特性进行比较。

### 这篇文章是写给谁的？

本文介绍了如何在 Elixir 中使用协议来实现多态功能。如果你以前从未听说过灵药，一定要去灵药网站查看更多关于这种令人兴奋的年轻语言的细节。

如果你熟悉 Java 中的面向对象编程(OOP ),这篇文章将有助于你了解 Java 类层次结构和 Elixir 协议之间的相似之处。

## 什么是多态性？

多态性是指我们的程序根据给定的数据类型或类分派给函数或方法。

例如，在 Java 中我们有能力定义一个接口，它只包含概述一些功能的方法定义。然后当我们定义一个类时，我们为一个接口的方法定义提供了*实现*。

如果一个方法的签名接受一个接口的实例，并调用该实例上的方法，那么在运行时，Java 会根据底层的*实现*类来决定将哪个方法*定义*分派给哪个方法。

这允许我们通过不将一个模块的实现绑定到另一个模块的实现来分离我们的代码。

## Java 类的层次结构

这里举个简单的例子。美国人用英寸测量身高，而世界上其他地方用厘米。我确信世界上的一些地方使用其他的测量单位，但是为了简单起见，我们将坚持这个假设。

让我们定义一个界面，用厘米或英寸来测量美国人或其他国家的人

```
public interface MeasuredPerson {
  double measureInInches();
  double measureInCentimetres();
} 
```

现在我们可以在几个类中实现这个接口，这些类代表来自美国和世界其他地方的人:

```
public class American implements MeasuredPerson {
  private String name;
  private double heightInInches;

  public American(String name, double heightInInches) {
    this.name = name;
    this.heightInInches = heightInInches;
  }

  public double measureInInches() { return heightInInches; }
  public double measureInCentimetres() { return heightInInches * 2.54; }
}

public class RestOfTheWorld implements MeasuredPerson {
  private String name;
  private double heightInCentimetres;

  public American(String name, double heightInCentimetres) {
    this.name = name;
    this.heightInCentimetres = heightInCentimetres;
  }

  public double measureInInches() { return heightInCentimetres * 0.39; }
  public double measureInCentimetres() { return heightInCentimetres; }
} 
```

现在我们已经有了几个可以使用的类，我们可以编写一个方法来接受一个`MeasuredPerson`并以英寸为单位打印出它们的高度。

```
public class Main {
  public static void main(string[] args) {
    American american = new American("John", 72);
    RestOfTheWorld restOfTheWorld = new RestOfTheWorld("Jean", 178);

    printInches(american);
    printInches(restOfTheWorld);
  }

  private void printInches(MeasuredPerson measuredPerson) {
    System.out.println("The person's height is: " + measuredPerson.measureInInches());
  }
} 
```

这个代码将以英寸为单位打印出美国人和其他国家的人的身高。方法`printInches`在运行时根据给定对象的底层类型将调用分派给`measureInInches`！

## 仙丹方案

Elixir 中的协议允许我们在程序中定义类似的关系。首先，我们定义一个协议，它是一组概述某些功能的函数定义。然后，当我们定义一个药剂类型的实现模块时，我们为协议的功能定义提供了*实现*。

药剂类型包括`List`、`Map`和`Keyword`，但是它们也可以包括我们定义的自定义结构。下面是一个两个人结构的例子，类似于我们上面定义的 Java 类:

```
defmodule Person.American do
  defstruct [:name, :height_in_inches]
end

defmodule Person.RestOfTheWorld do
  defstruct [:name, :height_in_centimetres]
end 
```

美国人用英寸测量身高，而世界上其他国家用厘米测量身高。如果我们想衡量一个人，而不用担心他是来自美国还是世界上的其他地方，会怎么样？

### `defprotocol`关键词

类似于我们如何在 Java 中定义一个接口，我们可以定义一个测量一个人身高的协议。协议需要一个测量单位，用户希望用它来测量给定的人。

```
defprotocol Person.Height do
  @doc """
  Accepts the person to measure as the first argument, and accepts either
  `:inches` or `:centimetres` as the second argument to indicate 
  which unit of measurement to return.
  """
  def measure(person, unit_of_measurement)
end 
```

注意，我们没有为我们的`measure/2`函数提供一个`do`块。下一节将展示我们如何为我们的新协议定义一个实现。

### `defimpl`关键词

以下模块使用`defimpl`关键字为我们的自定义 person 结构提供实现:

```
defimpl Person.Height, for: Person.American do
  def measure(%Person.American{height_in_inches: height_in_inches}, :inches) do
    height_in_inches
  end

  def measure(%Person.American{height_in_inches: height_in_inches}, :centimetres) do
    height_in_inches * 2.54
  end
end

defimpl Person.Height, for: Person.RestOfTheWorld do
  def measure(%Person.RestOfTheWorld{height_in_centimetres: height_in_centimetres}, :inches) do
    height_in_centimetres * 0.39
  end

  def measure(%Person.RestOfTheWorld{height_in_centimetres: height_in_centimetres}, :centimetres) do
    height_in_centimetres
  end
end 
```

`defimpl`获取一个协议模块(使用`defprotocol`定义)，该模块映射到我们想要为其提供实现的类型。因此，在我们的实现模块中定义的`measure/2`函数的第一个参数就是该类型的结构。

与我们的 Java 实现有一个关键区别:我们不是在`Person.American`和`Person.RestOfTheWorld`模块中实现行为，而是在一个单独的模块中实现行为。由于结构本身不能有行为(即你不能调用`american.measure(:centimetres)`)，结构必须改为传递给函数；我们如何做到这一点，见下文。

### 把一切联系在一起

现在我们已经为我们的`Person.American`和`Person.RestOfTheWorld`结构定义了几个实现模块，我们可以调用`Person.Height.measure/2`函数来以我们首选的度量单位度量给定的人！

```
%Person.American{name: "John", height_in_inches: 72}
|> Person.Height.measure(:centimetres)
|> IO.inspect()
# 182.88

%Person.RestOfTheWorld{name: "Jean", height_in_centimetres: 178}
|> Person.Height.measure(:inches)
|> IO.inspect()
# 70.0787 
```

## 协议的力量

我们在这里用了一个简单的例子来展示我们如何创建自己的协议。但这有什么大不了的？

使用协议是强大的，因为我们可以定义一个具有良好定义的行为的接口*一次*，然后基于我们的自定义结构在其他地方实现该行为。

与 Java 类层次结构相比，Elixir 协议的一个很大的优势是，我们可以为来自外部库的结构提供我们自己的协议实现。在 Java 中，你必须编辑其他库的源代码来实现你的自定义接口。但是在 Elixir 中，我们所要做的就是为一个类型(struct)提供一个实现，而不管是什么库/框架提供了那个类型！

### 一个熟悉的例子

在 Elixir 中，我们使用`Enum`模块来遍历列表和地图。我们作为第一个参数传递给`Enum`模块中函数的任何东西都必须有一个`Enumerable`协议的实现！要阅读更多关于这个[的内容，请查看`Enumerable`文档](https://hexdocs.pm/elixir/Enumerable.html)。

## 资源

我想为学习 Elixir 协议提供一些额外的资源，这样您就可以继续用传统的函数式语言探索这个 OOP 概念。

*   [酏剂方案](https://elixir-lang.org/getting-started/protocols.html):酏剂 lang 官方网站酏剂方案介绍。
*   [灵丹妙药`Protocol`医生](https://hexdocs.pm/elixir/Protocol.html):如果你想更深入地了解协议是如何工作的，这是一个很好的起点。长生不老药的文档写得太好了，以至于我通常不会去别的地方看。
*   [*编程仙丹> = 1.6*](https://www.amazon.com/Programming-Elixir-1-6-Functional-Concurrent-ebook/dp/B07DP8Y2GJ/ref=sr_1_1?crid=2E3ZEQ9KFZE4F&keywords=elixir+language&qid=1567959174&s=books&sprefix=elixir+lang%2Caps%2C128&sr=1-1) :这本书是我武器库中最好的工具之一。我无法为初学者和专家推荐一本更好的书。我一直把它作为参考。

我为我们这些熟悉 OOP 的人写了这篇文章，并展示了 Elixir 中的一个令人敬畏的特性，对于任何熟悉 Java 中 OOP 的人来说，这个特性都是如鱼得水。感谢阅读！