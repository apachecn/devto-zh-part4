# Java 和 TDD——让你的软件可预测

> 原文：<https://dev.to/brunooliveira/java-and-tdd-make-your-software-predictable-3daa>

**简介**

近年来，现代软件开发已经发生了很大的变化，不仅是本质上的，而且是由于越来越多的企业依赖软件来扩大他们的客户群和影响，使得编写安全和可预测的软件变得越来越重要。

可预测的软件更容易维护、调试和扩展，软件工程师采用一系列方法来确保他们的软件质量高。在这篇文章中，我将谈谈我对其中一个方面的看法:TDD——测试驱动开发的缩写。

TDD 本质上意味着在编写产品代码之前编写测试，并且只编写通过测试所需的最少代码。就像这样，您将使用小单元测试的形式来覆盖您的产品代码，以确保您的应用程序的核心功能是正确的，并且当事情发生变化时它仍然是正确的。

顾名思义，单元测试是测试(用测试术语来说就是断言)一个单元的行为在一组特定的输入条件或约束下符合预期的代码。我将把一个单元定义为在功能上不能被分割的最小的一段代码。在大多数语言中，这相当于一个函数或过程。

为了编写单元测试，我将在 IntelliJ IDE 中使用 JUnit 5。JUnit 是编写可重复测试的简单框架。

**我们的领域-购物车和产品**

我将在整篇文章中使用 Java，并引入一个简单的封闭域来说明我将讨论的概念，让我们考虑一下我们的域中有产品和购物车作为对象。一个购物车可以容纳多种产品，并且产品具有一些有趣的属性。下面是建模这些对象的基本 Java 类:

```
public class Product {
    private Integer id;
    private String name;
    private double price;

    public Product(int id, String name, double displayPrice){
        this.id = id;
        this.name = name;
        this.price = displayPrice;
                validateProduct();
    }

        private void validateProduct(){
            Validate.notNull(id);
            Validate.notNull(name);
        }

    public void applyDiscount(int discount){
        this.price = price*(1.0d-(1.0d*discount/100));
    }

    public double getPrice() {
        return price;
    }
} 
```

```
public class ShoppingCart {
  private maxCapacity;
  private List<Product> productsInCart;
  private totalPriceInCart;

  public ShoppingCart(maxCapacity) {
      this.maxCapacity = maxCapacity;
      validateCart();
  }

  private void validateCart() {
      if(this.maxCapacity <= 0) {
          throw new Exception("Shopping cart needs to be able to hold items");
      } 
  }

  public void addProduct(Product p){
     productsInCart.add(p);
  }

  public int getNumProductsInCart(){
     return productsInCart.size();
  }

  public double getTotalPriceInCart(){
     return productsInCart.stream()
.map(Product::getPrice).reduce(Double::sum);
  } 

} 
```

这些类中的每一个都有一个私有方法来确保我们的对象实例在构造时是有效的:我们不能对零容量、负容量或空容量的购物车做任何事情，并且作为一个业务需求，我们不能拥有 id 和名称为空的产品。

这意味着我们不需要担心对象的初始状态，同样，我们也不需要担心测试它们的内部状态。单元测试应该在我们的领域所强加的功能的上下文中使用，而不是关于对象的内部状态。在编写单元测试时，这是一个微妙但重要的区别:目标是测试功能和活动部分之间的交互，而不是内部状态。我见过开发人员编写无用的单元测试，却被引导去相信一切都很好。
旨在测试上下文中的功能。换句话说，只有 API 方法需要进行单元测试。

**测试核心功能**

作为我们的基本领域的核心功能的例子，让我们看两个案例:

当我们对产品打折时，它的折扣价需要比原价低。当我们将一件商品加入购物车时，它的商品数量就会增加一件。

```
class ProductTest {

    @Test
    void priceIsLowerAfterApplyingDiscount(){
        Product p = new Product(1,"testprod",10.0);
        double original = p.getPrice();
        p.applyDiscount(1); //1% discount
        assertEquals(9.9,p.getPrice());
    }
} 
```

```
class ShoppingCartTest {

    @Test
    void itemsInCartIncreaseByOneAfterAddingOneItem(){
        ShoppingCart cart = new ShoppingCart(10);
                Product p = new Product(1,"testprod",10.0);
                cart.addProduct(p);
                assertEquals(1,cart.getNumProductsInCart());

    }
} 
```

按照惯例，每个域类都可以有一个相应的测试类。测试类通常以单词 test 作为后缀，测试类中的每个方法都应该用来自 JUnit framework 的`@Test`注释进行注释，并有一个关于被测试内容的描述性名称。

这些测试中的每一个都给了我们信心，让我们相信被测试的类的内部是按照我们期望的那样工作的。如果在我们的类中有什么东西会以一种意想不到的方式改变，我们一旦运行测试就会知道它。

例如，让我们假设折扣价是基于百分比折扣的。

然而，需求发生了变化，现在要应用的折扣值实际上是一个绝对值，因此`applyDiscount(discount)`方法的内部实现将变为:

```
public void applyDiscount(int discount){
        this.price = price - discount;
    } 
```

有了好的单元测试，在这个变化完成后，我们可以重新运行它们，这将迅速向我们显示我们对折扣价格的计算现在失败了，这是一个很好的指标，表明我们的底层逻辑已经以我们可能或可能没有预测到的方式发生了变化，这是单元测试的主要优势:*它们提供了对意外变化的即时反馈，并允许您在代码库上操作大型重构，并且确信不会引入回归。*

这是我的第一篇文章，希望我会有动力经常在这里写作！