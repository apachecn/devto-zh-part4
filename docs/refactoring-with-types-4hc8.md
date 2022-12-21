# 使用类型重构

> 原文：<https://dev.to/maneeshchaturvedi/refactoring-with-types-4hc8>

## 介绍

在这篇文章中，我们将看看一些使用类型的重构技术。类型可以用来以一种细粒度的、定义良好的方式来表示领域。此外，类型可以用于以确保代码正确性的方式合并业务规则。这使得编写简单优雅的单元测试来确保代码的正确性成为可能。

## 使用类型重构

最近，在审查代码时，我遇到了下面的类，

```
public class OrderLine {
  private int quantity;
  private Double unitPrice;
  private Double listPrice;
  private Double tax;
  private Double charge;
  //Rest of the implementation
}
```

The code above is a classic example of a code smell called [*primitive obsession.*](https://sourcemaking.com/refactoring/smells/primitive-obsession) All the above parameters are represented using numbers. However are they just numbers. Is *UnitPrice* interchangeable with *ListPrice *or *Tax*. In domain driven design these are indeed distinct things and not just numbers. Ideally, we would like to have specific types to represent these concepts. The first level of refactoring is to create simple wrapper types for these classes

```
public class ListPrice {
    private ListPrice() {
    }
    private @Getter Double listPrice;
    public ListPrice(Double listPrice) {
        setListPrice(listPrice);
    }
    private void setListPrice(Double listPrice) {
        Objects.requireNonNull(listPrice, "list price can not be null");
        if (listPrice < 0) {
            throw new IllegalArgumentException("Invalid list price: "+listPrice);
        }
        this.listPrice = listPrice;
    }
}
```

```
public class UnitPrice {
    private UnitPrice() {
    }
    private @Getter Double unitPrice;
    public unitPrice(Double unitPrice) {
        setUnitPrice(unitPrice);
    }
    private void setUnitPrice(Double unitPrice) {
        Objects.requireNonNull(unitPrice, "unit price can not be null");
        if (unitPrice < 0) {
            throw new IllegalArgumentException("Invalid unit price: "+unitPrice);
        }
        this.unitPrice = unitPrice;
    }
}
```

This serves as a good starting point. We now have conceptual constructs for these. Any business rules which are required for a construct can now be wired within these constructs rather than being implemented in the container OrderLine class. However, if we observe, there is duplicate code to check that the listPrice and unitPrice should not be null or non-negative. This check would most probably be applied to quantity, tax and charge as well. Hence it makes sense to create a Type which represents a non-negative number concept.

```
public class NonNegativeDouble {
    private @Getter Double value;
    public NonNegativeDouble(Double value){
        this.setValue(value);
    }
    private void setValue(Double value) {
        Objects.requireNonNull(value,"Value cannot be null");
        if(value < 0){
            throw new IllegalArgumentException("Value has to be positive");
        }
    }
}
```

Now we can safely refactor the UnitPrice and ListPrice classes to use this new construct of a non-negative double.

```
public class UnitPrice {
    private UnitPrice() {
    }
    private
    @Getter
    NonNegativeDouble unitPrice;
    public UnitPrice(NonNegativeDouble unitPrice) {
        setUnitPrice(unitPrice);
    }
    private void setUnitPrice(NonNegativeDouble unitPrice) {
        this.unitPrice = unitPrice;
    }
}
```

A simple test to validate the non-negative constraints for UnitPrice

```
@Unroll
class UnitPriceSpec extends Specification {
    def "#text creation of Unit Price object with value - (#unitPriceValue)"() {
        given:
        def unitPrice
        when:
        boolean isExceptionThrown = false
        try {
            unitPrice = new UnitPrice(new NonNegativeDouble(unitPriceValue))
        } catch (Exception ex) {
            isExceptionThrown = true
        }
        then:
        assert isExceptionThrown == isExceptionExpected
        where:
        text        |   unitPriceValue      |   isExceptionExpected
        'Valid'     |   120                 |   false
        'Valid'     |   12.34               |   false
        'Valid'     |   0.8989              |   false
        'Valid'     |   12567652365.67667   |   false
        'Invalid'   |   0                   |   false
        'Invalid'   |   0.00000             |   false
        'Invalid'   |   -23.5676            |   true
        'Invalid'   |   -23478687           |   true
        'Invalid'   |   null                |   true
    }
}
```

Although this showcases a simple use-case for refactoring using types, it applies to a lot of constructs which are modeled as primitive types, like, Email, Names, Currency, Ranges and Date and Time.

## 重构——使用类型使非法状态不可表示

Another refactoring which provides a lot of value is to make illegal state unrepresentable in a domain model. As an example, consider the following java class.

```
public class CustomerContact {
    private @Getter EmailContactInfo emailContactInfo;
    private @Getter PostalContactInfo postalContactInfo;

    public CustomerContact(EmailContactInfo emailContactInfo, 
    PostalContactInfo postalContactInfo){
        setEmailContactInfo(emailContactInfo);
        setPostalContactInfo(postalContactInfo);
    }
    private void setEmailContactInfo(EmailContactInfo emailContactInfo){
        Objects.requireNonNull(emailContactInfo,"Email Contact 
                Info cannot be null");
        this.emailContactInfo = emailContactInfo;
    }
    private void setPostalContactInfo(PostalContactInfo postalContactInfo){
        Objects.requireNonNull(postalContactInfo,"Postal Contact 
                Info cannot be null");
        this.postalContactInfo = postalContactInfo;
    }

}

```

基于前面的重构，我们已经提取了域级别的构造 EmailContactInfo 和 PostalContactInfo。这些是真正的领域级结构，而不是实体字符串。

让我们假设一个简单的业务规则，它规定客户联系人必须有电子邮件联系信息或邮政联系信息。

这意味着至少应该有 EmailContactInfo 或 CustomerContactInfo 之一。两者也可以同时存在。然而，我们当前的实现要求两者都存在。

为了应用业务规则，第一次尝试可能如下所示。

```
public class CustomerContact {
    private @Getter Optional emailContactInfo;
    private @Getter Optional postalContactInfo;

    public CustomerContact(PersonName name, Optional emailContactInfo,
                           Optional postalContactInfo){
        setEmailContactInfo(emailContactInfo);
        setPostalContactInfo(postalContactInfo);
    }
    private void setEmailContactInfo(Optional emailContactInfo){
        this.emailContactInfo = emailContactInfo;
    }
    private void setPostalContactInfo(Optional postalContactInfo){
        this.postalContactInfo = postalContactInfo;
    }

}

```

现在，我们已经走得太远了。该规则要求 CustomerContact 应该至少有一个电子邮件或邮政联系人。但是，对于当前的实现，CustomerContact 可能没有任何一个。

简化业务规则导致下面的
客户联系人=电子邮件联系人或邮政联系人或电子邮件和邮政联系人

在函数式语言中，这样的条件可以使用 [sum 类型](https://chadaustin.me/2015/07/sum-types/)来设计。然而，在像 java 这样的语言中，没有对这些结构的一级支持。像 [javaSealedUnions](https://github.com/pakoito/JavaSealedUnions) 这样的库在 Java 中提供了对 Sum 和 Union 类型的支持。

使用 JavaSealedUnions，我们可以实现如下业务规则

```
public abstract class CustomerContact implements Union2 {

    public abstract boolean valid();

    public static CustomerContact email(String emailAddress) {
        return new EmailContact(emailAddress);
    }

    public static CustomerContact postal(String postalAddress) {
        return new PostalContact(postalAddress);
    }
}

class EmailContact extends CustomerContact {

    private final String emailAddress;

    EmailContact(String emailAddress) {
        this.emailAddress = emailAddress;        
    }

    public boolean valid() {
        return /* some logic here */
    }

    public void continued(Consumer continuationLeft, Consumer continuationRight) {
        continuationLeft.call(value);
    }

    public  T join(Function mapLeft, Function mapRight) {
        return mapLeft.call(value);
    }
}

class PostalContact extends CustomerContact {

    private final String address;

    PostalContact(String address) {
        this.address = address;

    }

    public boolean valid() {
        return /* some logic here */
    }

    public void continued(Consumer continuationLeft, Consumer continuationRight) {
        continuationRight.call(value);
    }

    public  T join(Function mapLeft, Function mapRight) {
        return mapRight.call(value);
    }
}

// Example

CustomerContact customerContact = getCustomerContact();
if (customerContact.valid()) {
    customerContact.continued(customerContactService::byEmail(), customerContactService::byPostalAddress())
}

```

这篇文章展示了从类型的角度思考可以帮助设计更简洁的一些方法。从类型的角度考虑也有助于避免业务规则的模糊性。上面显示的方法也可以在其他场景中使用，以捕获允许的状态或成功和失败的情况。

可供参考的几个很棒的帖子是

[为什么类型优先开发很重要](http://tomasp.net/blog/type-first-development.aspx/)

[F #型系统的领域驱动设计](https://fsharpforfunandprofit.com/ddd/)