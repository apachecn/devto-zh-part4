# Java 可选& Vavr 选项:基本介绍

> 原文：<https://dev.to/iuriimednikov/java-optional-vavr-option-an-essential-introduction-10cb>

你好。今天我想讨论一些基本的 Java 话题——可选类的使用，并将其与 Vavr 库中的一个可选类进行比较。Optional 最初是在 Java 8 中引入的，它被定义为“一个可能包含也可能不包含非空值的容器对象”。当执行代码导致的不是结果，而是 *null* 值时，开发人员利用选项来避免在某些地方进行空值检查，这可能会导致 **NullPointerException** 。在这种情况下，Optional 为我们提供了一些奇特的功能，但并不是所有的功能都在第 8 版中引入了，有些功能需要 Java 11。处理这些问题的另一种方法是 Vavr 的选项类。

在这篇文章中，我们将学习如何使用 Java 的可选类，然后将它与 Vavr 的可选类进行比较。注意，该代码至少需要 Java 11，并且是用 Vavr *0.10.2* 测试的。

## 介绍 Java 可选

可选的概念并不新鲜，已经在函数式编程语言中实现了，比如 Haskell 或 Scala。当方法调用可能返回未知值或不存在的值(例如空值)时，它被证明是非常有用的。让我们看看如何处理它。

### 可选的创建

首先，我们需要获得一个可选的实例。有几种方法可以做到这一点——此外，我们还可以创建一个*空的*可选的。检查第一种方法——从价值中创造，这很简单:

```
Optional<Integer> four = Optional.of(Integer.valueOf(4));
if (four.isPresent){
    System.out.println("Hoorayy! We have a value");
} else {
    System.out.println("No value");
} 
```

Enter fullscreen mode Exit fullscreen mode

我们从一个整数值 4 构建一个可选的，这意味着总应该有一个值，并且不能为空，但这只是一个例子。我们用 **ifPresent()** 方法检查值的存在或不存在。你可以注意到，**四个**不是一个整数，它是一个容器，*在*里面存放整数。当我们确定这个值在里面时，我们可以用 **get()** 方法“解包”它。具有讽刺意味的是，如果我们在不检查的情况下使用 **get()** ，我们可以用 NoSuchElementException 结束。

获取可选的另一种方法是使用流。几个[终端流的方法](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#StreamOps)返回选项，因此我们可以操作它们并检查是否存在，例如:

*   findAny
*   findFirst
*   最大
*   部
*   减少

查看下面的代码片段:

```
Optional<Car> car = cars.stream().filter(car->car.getId().equalsIgnoreCase(id)).findFirst(); 
```

Enter fullscreen mode Exit fullscreen mode

下一个选项是从代码中创建可选的，即*可能产生空值*，例如从可空值:

```
Optional<Integer> nullable = Optional.ofNullable(client.getRequestData()); 
```

Enter fullscreen mode Exit fullscreen mode

最后我们可以创建一个*空*可选:

```
Optional<Integer> nothing = Optional.empty(); 
```

Enter fullscreen mode Exit fullscreen mode

### 如何使用可选

只要我们获得了可选的，我们就可以使用它。一个最普遍的情况是在 Spring 存储库中使用它来通过 id 查找一个记录，这样我们就可以在 Optional 上构建我们的逻辑并避免空检查(顺便说一下，Spring 也支持 Vavr 选项)。比方说，我们有一个图书仓库，想要找到一本书。

```
Optional<Book> book = repository.findOne("some id"); 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们可以执行一些逻辑以防万一，如果书*被呈现*。在上一节中，我们用 if-else 实现了这一点，但我们不需要这样做:Optional 为我们提供了一个方法，该方法接受一个带有对象
的*消费者*

```
repository.findOne("some id").ifPresent(book -> {}); 
```

Enter fullscreen mode Exit fullscreen mode

或者更简单，我们可以用方法引用来写:

```
repository.findOne("some id").ifPresent(System.out::println); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们在存储库中没有一本书，我们可以用 **ifPresentOrElseGet** 方法:
提供一些可选的回调

```
repository.findOne("some id").ifPresentOrElseGet(book->{
    // if value is presented
}, ()->{
    // if value is absent
}); 
```

Enter fullscreen mode Exit fullscreen mode

或者，如果没有给出运算结果，我们可以得到另一个值:

```
Book result = repository.findOne("some id").orElse(defaultBook); 
```

Enter fullscreen mode Exit fullscreen mode

然而，对于期权，我们需要记住可能的缺点。在最后一个例子中，我们“保证”自己无论如何都会得到一本书，要么它存在于底层存储库中，要么来自**或其他**。但是如果这个默认值不是*常数*，还需要一些复杂的方法呢？首先，Java **反正**评估 **findOne** 。然后，它必须处理**或其他**方法。是的，如果它只是一个默认的常量值，这是可以的，但它也可能像我之前说的那样耗时。

## 让我们举个例子

让我们创建一个简单的例子来检查如何实际使用可选类和选项类。我们将有一个*仓库*，它将根据提供的 ID(例如，车牌号码)找到一辆汽车。然后我们会看到如何操纵期权和期权。

## 先添加一些代码

从 POJO 级*车*开始。它遵循*不可变的*模式，所以所有字段都是 final，我们只有 getters，没有 setters。所有数据都是在初始化期间提供的。

```
public class Car {

    private final String name;
    private final String id;
    private final String color;

    public Car (String name, String id, String color){
        this.name = name;
        this.id = id;
        this.color = color;
    }

    public String getId(){
        return id;
    }

    public String getColor() {
        return color;
    }

    public String getName() {
        return name;
    }

    @Override
    public String toString() {
        return "Car "+name+" with license id "+id+" and of color "+color;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

第二个要创建的是*仓库*类。通过 Id 查找汽车有两种选择——使用旧的方法，结果可能为空，以及使用可选的 Spring 存储库。

```
public class CarRepository {

    private List<Car> cars;

    public CarRepository(){
       getSomeCars();
    }

    Car findCarById(String id){
        for (Car car: cars){
            if (car.getId().equalsIgnoreCase(id)){
                return car;
            }
        }
        return null;
    }

    Optional<Car> findCarByIdWithOptional(String id){
        return cars.stream().filter(car->car.getId().equalsIgnoreCase(id)).findFirst();
    }

    private void getSomeCars(){
        cars = new ArrayList<>();
        cars.add(new Car("tesla", "1A9 4321", "red"));
        cars.add(new Car("volkswagen", "2B1 1292", "blue"));
        cars.add(new Car("skoda", "5C9 9984", "green"));
        cars.add(new Car("audi", "8E4 4321", "silver"));
        cars.add(new Car("mercedes", "3B4 5555", "black"));
        cars.add(new Car("seat", "6U5 3123", "white"));
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们还在初始化期间用一些模拟汽车填充我们的存储库，所以我们没有任何底层数据库。我们需要避免复杂性，让我们专注于可选性和选项，而不是存储库。

### 用 Java 可选找车

用 JUnit 创建一个新的测试(如果你没有这方面的经验，我还有一个关于 JUnit 5 的极好的[教程)。](https://www.mednikov.net/tutorials/how-to-start-with-unit-testing-in-java-a-quick-introduction-to-junit-5/) 

```
@Test
void getCarById(){
    Car car = repository.findCarById("1A9 4321");
    Assertions.assertNotNull(car);
    Car nullCar = repository.findCarById("M 432 KT");
    Assertions.assertThrows(NullPointerException.class, ()->{
        if (nullCar == null){
            throw new NullPointerException();
        }
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码片段演示了旧的方法。我们发现了一辆捷克车牌为 **1A9 4321** 的汽车，并检查了它的存在。然后我们发现一辆车不见了，因为它有俄罗斯牌照，而我们的资料库里只有捷克牌照。是 *null* ，所以可能会导致 NullPointerException。

接下来，让我们转到 Java 选项。第一步是使用返回 Optional:
的专用方法从存储库中获取可选实例

```
@Test
void getCarByIdWithOptional(){
    Optional<Car> tesla = repository.findCarByIdWithOptional("1A9 4321");
    tesla.ifPresent(System.out::println);
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我们使用 **findCarByIdWithOptional** 方法，然后打印一辆汽车(如果它存在的话)。如果你运行它，你会得到下面的输出:

```
Car tesla with license id 1A9 4321 and of color red 
```

Enter fullscreen mode Exit fullscreen mode

但是如果我们的代码中没有那个特殊的方法呢？在这种情况下，我们可以从可能返回 null 值的方法中获取 Optional。它叫做*可空* :

```
Optional<Car> nothing = Optional.ofNullable(repository.findCarById("5T1 0965"));
Assertions.assertThrows(NoSuchElementException.class, ()->{
    Car car = nothing.orElseThrow(()->new NoSuchElementException());
}); 
```

Enter fullscreen mode Exit fullscreen mode

在这段代码中，我们使用了另一种方式。我们创建可选的 from **findCarById** ，如果没有找到汽车，它可以返回 *null* 。当想要的车牌为 **5T1 0965** 的汽车出现时，我们手动使用 **orElseThrow** 方法抛出 NoSuchElementException。另一种情况是，如果所请求的数据在存储库中不可用，则使用带有某个默认值的**或 else**:

```
Car audi = repository.findCarByIdWithOptional("8E4 4311")
            .orElse(new Car("audi", "1W3 4212", "yellow"));
   if (audi.getColor().equalsIgnoreCase("silver")){
     System.out.println("We have silver audi in garage!");
   } else {
     System.out.println("Sorry, there is no silver audi, but we called you a taxi");
} 
```

Enter fullscreen mode Exit fullscreen mode

好吧，我们车库里没有银色奥迪，只能叫出租车了！

### 使用 Vavr 选项查找汽车

Vavr 选项是处理这些任务的另一种方式。首先，用依赖管理在你的项目中安装 Vavr(我用的是 Maven):

```
<dependency>
   <groupId>io.vavr</groupId>
   <artifactId>vavr</artifactId>
   <version>0.10.2</version>
</dependency> 
```

Enter fullscreen mode Exit fullscreen mode

简而言之，Vavr 有类似的 API 来创建选项实例。我们可以从 nullable:
中创建选项

```
Option<Car> nothing = Option.of(repository.findCarById("T 543 KK")); 
```

Enter fullscreen mode Exit fullscreen mode

或者我们可以用 **none** 静态方法:
创建一个*空的*容器

```
Option<Car> nullable = Option.none(); 
```

Enter fullscreen mode Exit fullscreen mode

此外，还有一种从 Java Optional 创建 Option 的方法！看看下面的代码片段:

```
Option<Car> result = Option.ofOptional(repository.findCarByIdWithOptional("5C9 9984")); 
```

Enter fullscreen mode Exit fullscreen mode

与 Vavr 选项，我们可以使用相同的 API 像可选的和完成上述任务。例如，我们可以用类似的方式设置一个默认值:

```
Option<Car> result = Option.ofOptional(repository.findCarByIdWithOptional("5C9 9984"));
Car skoda = result.getOrElse(new Car("skoda", "5E2 4232", "pink"));
System.out.println(skoda); 
```

Enter fullscreen mode Exit fullscreen mode

或者我们可以抛出一个异常，基于缺少请求的数据:

```
Option<Car> nullable = Option.none();
Assertions.assertThrows(NoSuchElementException.class, ()->{
    nullable.getOrElseThrow(()->new NoSuchElementException());
}); 
```

Enter fullscreen mode Exit fullscreen mode

或者，我们可以在数据不可用时采取行动:

```
nullable.onEmpty(()->{
    ///runnable
}) 
```

Enter fullscreen mode Exit fullscreen mode

如果我们需要基于数据的存在执行一个动作，就像我们对 Optional 的 **ifPresent** 所做的那样，该怎么办？我们可以用几种方法做到这一点。在 Optional 中有一个与 **isPresent** 相等的方法，即在 Option 中调用 **isDefined** :

```
if (result.isDefined()){
    // do something
} 
```

Enter fullscreen mode Exit fullscreen mode

然而，我们使用选项来消除 if-else 结构。我们能像可选的那样浮动吗？我们可以使用 **peek** :
基于存在进行操作

```
result.peek(val -> System.out.println(val)).onEmpty(() -> System.out.println("Result is missed")); 
```

Enter fullscreen mode Exit fullscreen mode

此外，Vavr Option 中还有一些其他非常有用的方法，与内置的可选类相比，它们会使您的代码更具功能性。所以我鼓励你花些时间探索 Vavr 选项 [javadocs](https://static.javadoc.io/io.vavr/vavr/0.9.2/io/vavr/control/Option.html) 并尝试这些 API。我可以注意到一些很酷的东西，比如**地图**、**狭窄**、**狭窄**和**当**你肯定需要检查一下。此外，Vavr 选装件是 Vavr 系列的一部分，与 Vavr 其他类别紧密集成，在真空中与选装件进行此类比较是不正确的。所以，我也会写一些关于其他 Vavr 主题的帖子，比如 Try，Collections，Streams。别忘了注册更新！

## 结论

在这篇文章中，我们讨论了 Java 中的可选类。可选的概念并不新鲜，已经在函数式编程语言中实现了，比如 Haskell 或 Scala。当方法调用可能返回未知值或不存在的值(例如空值)时，它被证明是非常有用的。让我们看看如何处理它。我们研究了它的 API，并用可选的逻辑做了一些查找汽车和操作结果的例子。然后，我们发现了 Optional 的一个替代方案——Vavr 的 Option，并描述了它的方法。

## 阅读更多

*   何塞·保马德。*选项:模式和良好实践* (2016)甲骨文社区目录，[点击此处](https://community.oracle.com/docs/DOC-991686)
*   默文·麦克赖特和穆罕默德·艾明·托克。*看看 Java 中可选的数据类型和使用它时的一些反模式* (2019) FreeCodeCamp，[阅读这里](https://www.freecodecamp.org/news/optional-in-java-and-anti-patterns-using-it-7d87038362ba/)
*   拉多斯瓦夫·诺瓦克。*用 Vavr 进行 Java 函数式编程* (2018)实用主义者，[在此阅读](https://blog.pragmatists.com/functional-programming-in-java-with-vavr-9861e704301c)