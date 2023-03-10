# 什么是序列化？关于 Java 序列化，您需要知道的一切都用示例进行了解释

> 原文：<https://dev.to/njnareshjoshi/what-is-serialization-everything-you-need-to-know-about-java-serialization-explained-with-example-9mj>

在上一篇文章中，我们讨论了在 java 中创建对象的 5 种不同方法，我解释了如何去序列化一个序列化的对象来创建一个新的对象，在这篇博客中，我将详细讨论序列化和反序列化。

下面我们将以`Employee`类对象为例进行说明

```
// If we use Serializable interface, static and transient variables do not get serialize
class Employee implements Serializable {

    // This serialVersionUID field is necessary for Serializable as well as Externalizable to provide version control,
    // Compiler will provide this field if we do not provide it which might change if we modify the class structure of our class, and we will get InvalidClassException,
    // If we provide value to this field and do not change it, serialization-deserialization will not fail if we change our class structure.
    private static final long serialVersionUID = 2L;

    private final String firstName; // Serialization process do not invoke the constructor but it can assign values to final fields
    private transient String middleName; // transient variables will not be serialized, serialised object holds null
    private String lastName;
    private int age;
    private static String department; // static variables will not be serialized, serialised object holds null

    public Employee(String firstName, String middleName, String lastName, int age, String department) {
        this.firstName = firstName;
        this.middleName = middleName;
        this.lastName = lastName;
        this.age = age;
        Employee.department = department;

        validateAge();
    }

    private void validateAge() {
        System.out.println("Validating age.");

        if (age < 18 || age > 70) {
            throw new IllegalArgumentException("Not a valid age to create an employee");
        }
    }

    @Override
    public String toString() {
        return String.format("Employee {firstName='%s', middleName='%s', lastName='%s', age='%s', department='%s'}", firstName, middleName, lastName, age, department);
    }

  // Custom serialization logic,
    // This will allow us to have additional serialization logic on top of the default one e.g. encrypting object before serialization
    private void writeObject(ObjectOutputStream oos) throws IOException {
        System.out.println("Custom serialization logic invoked.");
        oos.defaultWriteObject(); // Calling the default serialization logic
    }

    // Custom deserialization logic
    // This will allow us to have additional deserialization logic on top of the default one e.g. decrypting object after deserialization
    private void readObject(ObjectInputStream ois) throws IOException, ClassNotFoundException {
        System.out.println("Custom deserialization logic invoked.");

        ois.defaultReadObject(); // Calling the default deserialization logic

        // Age validation is just an example but there might some scenario where we might need to write some custom deserialization logic
        validateAge();
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

## 什么是序列化和反序列化

在 Java 中，我们创建几个对象，这些对象会相应地存活和死亡，当 JVM 死亡时，每个对象肯定会死亡，但有时我们可能希望在几个 JVM 之间重用一个对象，或者我们可能希望通过网络将一个对象传输到另一台机器。

嗯，**序列化**允许我们将对象的状态转换成字节流，然后可以保存到本地磁盘上的文件中，或者通过网络发送到任何其他机器。而**反序列化**允许我们逆转这个过程，这意味着再次将序列化的字节流重新转换为对象。

简而言之，对象**序列化**是将对象的状态保存到一个字节序列的过程，而**反序列化**是从这些字节重建对象的过程。一般来说，完整的过程被称为**序列化**，但我认为为了更加清晰起见，最好将两者分开。

> 序列化过程是独立于平台的，在一个平台上序列化的对象可以在不同的平台上反序列化。

为了将我们的对象序列化和反序列化为一个文件，我们需要调用`ObjectOutputStream.writeObject()`和`ObjectInputStream.readObject()`，如下面的代码所示:

```
public class SerializationExample {
    public static void main(String[] args) throws IOException, ClassNotFoundException {
        Employee empObj = new Employee("Shanti", "Prasad", "Sharma", 25, "IT");
        System.out.println("Object before serialization  => " + empObj.toString());

        // Serialization
        serialize(empObj);

        // Deserialization
        Employee deserialisedEmpObj = deserialize();
        System.out.println("Object after deserialization => " + deserialisedEmpObj.toString());
    }

    // Serialization code
    static void serialize(Employee empObj) throws IOException {
        try (FileOutputStream fos = new FileOutputStream("data.obj");
             ObjectOutputStream oos = new ObjectOutputStream(fos))
        {
            oos.writeObject(empObj);
        }
    }

    // Deserialization code
    static Employee deserialize() throws IOException, ClassNotFoundException {
        try (FileInputStream fis = new FileInputStream("data.obj");
             ObjectInputStream ois = new ObjectInputStream(fis))
        {
            return (Employee) ois.readObject();
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 只有实现了 Serializable 的类才能被序列化

类似于序列化中用于 [Java 克隆](https://www.programmingmitra.com/2016/11/Java-Cloning-Types-of-Cloning-Shallow-Deep-in-Details-with-Example.html)的 Cloneable 接口，我们有一个 Serializable 标记接口，它像 JVM 的标志一样工作。任何直接或通过其父类实现`Serializable`接口的类都可以序列化，而不实现`Serializable`的类不能序列化。

Java 的默认序列化过程是完全递归的，所以每当我们试图序列化一个对象时，序列化过程都会尝试用我们的类序列化所有字段(原语和引用)(除了`static`和`transient`字段)。

> 当一个类实现了`Serializable`接口时，它的所有子类也是可序列化的。但是当一个对象有对另一个对象的引用时，这些对象必须单独实现`Serializable`接口。如果我们的类只有一个对非`Serializable`类的引用，那么 JVM 将抛出`NotSerializableException`。

## 为什么 Serializable 不是通过对象实现的？

现在出现了一个问题，如果序列化是非常基本的功能，任何没有实现`Serializable`的类都不能被序列化，那么为什么 Serializable 不是由`Object`自己实现的呢？通过这种方式，我们所有的对象都可以被默认序列化。

`Object`类没有实现`Serializable`接口，因为我们可能不想序列化所有的对象，例如序列化一个线程没有任何意义，因为在我的 JVM 中运行的线程会使用我的系统内存，持久化它并试图在你的 JVM 中运行它没有任何意义。

## 瞬态和静态字段不会被序列化

如果我们想序列化一个对象，但不想序列化一些特定的字段，那么我们可以将这些字段标记为 **transient** 。

所有的**静态**字段属于类而不是对象，序列化过程序列化对象，所以静态字段不能被序列化。

> 1.  Serialize the access modifiers of fields that don't care, such as `private`. All non-transient and non-static fields are considered as part of the persistent state of the object and are suitable for serialization.
> 2.  We can only assign values to the final field in the constructor. The serialization process will not call any constructor, but it can still assign values to the final field.

## 什么是 serialVersionUID，为什么要声明？

假设我们有一个类，我们将它的对象序列化到磁盘上的一个文件中，由于一些新的需求，我们在类中添加/删除了一个字段。现在，如果我们试图反序列化已经序列化的对象，我们将得到`InvalidClassException`，为什么？

我们得到它是因为默认情况下，JVM 将一个版本号与每个可序列化的类相关联，以控制类的版本控制。它用于验证序列化和反序列化的对象具有相同的属性，因此与反序列化兼容。版本号保存在名为`serialVersionUID`的字段中。如果一个可序列化的类没有声明一个`serialVersionUID`，JVM 会在运行时自动生成一个。

如果我们改变我们的类结构，例如删除/添加字段，版本号也会改变，根据 JVM，我们的类与序列化对象的类版本不兼容。这就是我们得到异常的原因，但是如果你仔细想想，为什么仅仅因为我添加了一个字段就要抛出异常呢？不能将字段设置为默认值，然后下次再写出吗？

是的，这可以通过手动提供`serialVersionUID`字段来完成，并确保它总是相同的。强烈建议每个可序列化的类声明它的`serialVersionUID`,因为生成的类依赖于编译器，因此可能会导致意外的 *InvalidClassExceptions。*

> 您可以使用 JDK 发行版附带的一个名为`serialver`的实用程序来查看默认情况下该代码是什么(默认情况下它只是对象的散列代码)。

## 用 writeObject 和 readObject 方法定制序列化和反序列化

JVM 在默认的序列化过程中对序列化对象有完全的控制权，但是使用默认的序列化过程有很多缺点，其中一些是:

1.  它不能处理不可序列化的字段的序列化。
2.  反序列化过程在创建对象时不调用构造函数，因此它不能调用构造函数提供的初始化逻辑。

但是我们可以在 Java 类中覆盖这个默认的序列化行为，并提供一些额外的逻辑来增强正常的过程。这可以通过在我们想要序列化的类中提供两个方法`writeObject`和`readObject`来实现:

```
// Custom serialization logic will allow us to have additional serialization logic on top of the default one e.g. encrypting object before serialization
private void writeObject(ObjectOutputStream oos) throws IOException {
  // Any Custom logic
 oos.defaultWriteObject(); // Calling the default serialization logic
  // Any Custom logic
}

// Custom deserialization logic will allow us to have additional deserialization logic on top of the default one e.g. decrypting object after deserialization
private void readObject(ObjectInputStream ois) throws IOException, ClassNotFoundException {
 // Any Custom logic
 ois.defaultReadObject(); // Calling the default deserialization logic
  // Any Custom logic
} 
```

Enter fullscreen mode Exit fullscreen mode

将两个方法都声明为私有是必要的(公共方法将不起作用),所以除了 JVM 之外，其他任何东西都看不到它们。这也证明了这两个方法都没有被继承、重写或重载。JVM 自动检查这些方法，并在序列化-反序列化过程中调用它们。JVM 可以调用这些私有方法，但其他对象不能。因此，类的完整性得到了维护，序列化协议可以继续正常工作。

即使提供了那些专用的私有方法，对象序列化通过调用`ObjectOutputStream.writeObject()`或`ObjectInputStream.readObject()`以同样的方式工作。

对`ObjectOutputStream.writeObject()`或`ObjectInputStream.readObject()`的调用启动了序列化协议。首先，检查对象以确保它实现了`Serializable`，然后检查是否提供了这些私有方法。如果提供了它们，stream 类将作为参数传递给这些方法，让代码控制它的使用。

我们可以从这些方法中调用`ObjectOutputStream.defaultWriteObject()`和`ObjectInputStream.defaultReadObject()`来获得默认的序列化逻辑。这些调用就像它们听起来的那样——它们执行序列化对象的默认读写，这很重要，因为我们没有替换正常的过程，我们只是增加了它。

这些私有方法可以用于您在序列化过程中想要进行的任何定制，例如，可以将加密添加到输出中，将解密添加到输入中(注意，字节以明文形式写入和读取，完全没有混淆)。它们可以用来向流中添加额外的数据，也许是公司版本控制代码，这种可能性是无限的。

除了 writeObject 和 readObject 方法，我们还可以利用其他的[序列化魔术方法](https://www.programmingmitra.com/2019/08/java-serialization-magic-methods-and-their-uses-with-example.html)来获得一些额外的功能。

## 停止序列化和反序列化

假设我们有一个从其父类获得序列化能力的类，这意味着我们的类从另一个实现`Serializable`的类扩展而来。

这意味着任何人都可以序列化和反序列化我们类的对象。但是，如果我们不希望我们的类被序列化或反序列化，例如，我们的类是单例的，我们想防止任何新的对象创建，记住[反序列化过程创建一个新的对象](https://www.programmingmitra.com/2016/05/different-ways-to-create-objects-in-java-with-example.html)。

为了停止我们类的序列化，我们可以再次使用上面的私有方法来抛出`NotSerializableException`。任何序列化或反序列化我们的对象的尝试都会导致抛出异常。由于这些方法被声明为`private`，没有人能够覆盖你的方法并改变它们。

```
private void writeObject(ObjectOutputStream oos) throws IOException {
  throw new NotSerializableException("Serialization is not supported on this object!");
}

private void readObject(ObjectInputStream ois) throws IOException, ClassNotFoundException {
  throw new NotSerializableException("Serialization is not supported on this object!");
} 
```

Enter fullscreen mode Exit fullscreen mode

然而，这违反了利斯科夫替代原理。并且 [writeReplace 和 readResolve 方法可以用来](https://www.programmingmitra.com/2019/08/java-serialization-magic-methods-and-their-uses-with-example.html)实现类似单例的行为。这些方法用于允许对象在 ObjectStream 中为自己提供替代表示。简而言之，readResolve 可用于更改通过 readObject 方法反序列化的数据，writeReplace 可用于更改通过 writeObject 序列化的数据。

Java [序列化也可以用来深度克隆一个对象](https://www.programmingmitra.com/2019/08/how-to-deep-clone-an-object-using-java-in-memory-serialization.html)。Java 克隆是 Java 社区中最有争议的话题，它确实有它的缺点，但它仍然是创建对象副本的最流行和最容易的方法，直到该对象完全满足 Java 克隆的强制条件。我在一篇 3 篇文章的 [Java 克隆系列](https://programmingmitra.blogspot.in/search/label/Java%20Cloning)中详细介绍了克隆，其中包括类似 [Java 克隆和克隆类型(浅层和深层)的文章，并附有示例](https://programmingmitra.blogspot.in/2016/11/Java-Cloning-Types-of-Cloning-Shallow-Deep-in-Details-with-Example.html)、 [Java 克隆——复制构造函数与克隆的比较](https://programmingmitra.blogspot.in/2017/01/Java-cloning-copy-constructor-versus-Object-clone-or-cloning.html)、 [Java 克隆——即使复制构造函数也不够用](https://programmingmitra.blogspot.in/2017/01/java-cloning-why-copy-constructors-are-not-sufficient-or-good.html)，如果您想了解更多关于克隆的知识，请继续阅读。

## 结论

1.  **序列化**是将对象的状态保存到字节序列中的过程，然后可以存储在文件中或通过网络发送，而**反序列化**是从这些字节中重建对象的过程。
2.  只有`Serializable`接口的子类可以被序列化。
3.  如果我们的类没有实现`Serializable`接口或者引用了一个非`Serializable`类，那么 JVM 将抛出`NotSerializableException`。
4.  所有的`transient`和`static`字段不会被序列化。
5.  `serialVersionUID`用于验证序列化和反序列化的对象具有相同的属性，因此与反序列化兼容。
6.  我们应该在我们的类中创建一个`serialVersionUID`字段，这样如果我们改变我们的类结构(添加/删除字段), JVM 将不会通过`InvalidClassException`。如果我们不提供它，JVM 会提供一个，当我们的类结构改变时，它也会改变。
7.  我们可以通过提供`writeObject`和`readObject`方法的实现来覆盖 Java 类内部的默认序列化行为。
8.  我们可以从`writeObject`和`readObject`方法中调用`ObjectOutputStream.defaultWriteObject()`和`ObjectInputStream.defaultReadObject`来获得默认的序列化和反序列化逻辑。
9.  如果我们不希望我们的类被序列化或反序列化，我们可以从`writeObject`和`readObject`抛出`NotSerializableException`异常。

Java 序列化过程可以使用`Externalizable`接口进一步定制和增强，我在[如何使用外部化接口](https://www.programmingmitra.com/2019/08/how-to-customize-serialization-in-java-by-using-externalizable-interface.html)定制 Java 序列化中已经解释过了。

我还写了一系列文章解释有效 Java 的第 74 到 78 项，其中进一步讨论了如何增强 Java 序列化过程，如果您愿意，请继续阅读。

您可以在这个 [Github 资源库](https://github.com/njnareshjoshi/exercises/blob/master/src/org/programming/mitra/exercises/SerializationExample.java)上找到本文的完整源代码，请随时提供您的宝贵反馈。