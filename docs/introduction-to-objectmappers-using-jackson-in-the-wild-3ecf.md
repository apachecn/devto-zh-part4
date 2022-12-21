# 对象映射器简介:在野外使用 Jackson

> 原文：<https://dev.to/brunooliveira/introduction-to-objectmappers-using-jackson-in-the-wild-3ecf>

**简介**

这篇文章的动机是我最近在现实世界中做的一些工作，涉及到 [Jackson ObjectMapper](https://fasterxml.github.io/jackson-databind/javadoc/2.7/com/fasterxml/jackson/databind/ObjectMapper.html) ，在这篇文章中，我将尝试解释为什么它很重要，以及它的一些很酷的技巧和怪癖。我还会列举一些它的真实使用场景。

**什么是杰克逊对象映射器**

引用文档:

> ObjectMapper 提供了从基本 POJOs(普通旧 Java 对象)或通用 JSON 树模型(JsonNode)读取和写入 JSON 的功能，以及执行转换的相关功能。它也是高度可定制的，既可以处理不同风格的 JSON 内容，也可以支持更高级的对象概念，比如多态和对象标识。ObjectMapper 还充当更高级的 ObjectReader 和 ObjectWriter 类的工厂。Mapper(及其构造的 ObjectReaders、ObjectWriters)将使用 JsonParser 和 JsonGenerator 的实例来实现 JSON 的实际读/写。请注意，尽管大多数读取和写入方法都是通过此类公开的，但一些功能仅通过 ObjectReader 和 ObjectWriter 公开:具体来说，较长值序列的读取/写入仅可通过 object reader . read values(InputStream)和 object writer . write values(output stream)实现。

这是一个很大的过程，但是，关键的要点就在开始:

ObjectMapper 提供从基本 POJOs(普通旧 Java 对象)或通用 JSON 树模型(JsonNode)读取和写入 JSON 的功能，以及执行转换的相关功能。

这是非常强大的，有几个原因:

*   REST APIs 通常从它们的端点返回 JSON 响应，有多个库可以从 JSON 中提取信息
*   JSON 是一种公认的标准格式，是通用的，可以作为字符串传递，由标准文本编辑器读取，等等。换句话说:它简单、功能多样，使用和理解起来非常非常有用

从这些对象的 Java 表示中抽象出来的力量在于，它封装了它们，并且在直接交换关于它们的信息时不会暴露它们，这很好。

作为一个例子，web 前端只需要关心它所消费的领域模型的文本表示，以便将其显示给最终用户。JSON 是这项工作的完美格式。

**一个示例域模型**

让我们看看 ObjectMapper 如何处理一个好领域中的简单示例:一家餐馆、一辆快餐车、厨师和美食:)

我们的餐厅将有每日食物菜单，厨师准备这些菜单，顾客参观餐厅。这些实体中的每一个都可以用下面的 Java 类来表示:

```
public class FoodtruckOwner implements Chef {

    private final String name;
    private MainDish mainDish;
    private String establishment;

    public FoodtruckOwner(String name, MainDish mainDish, String establishmentType){
        this.name = name;
        this.mainDish = mainDish;
        this.establishment=establishmentType;
    }

    public void setChefsMainDish(MainDish mainDish) {
        this.mainDish=mainDish;
    }

    public MainDish getChefsMainDish() {
        return mainDish;
    }

    public String getEstablishmentType() {
        return establishment;
    }

    public void setEstablishmentType(String typeOfEstablishment) {
        this.establishment=typeOfEstablishment;
    }
} 
```

```
public class RestaurantChef implements Chef{

    private final String name;
    private MainDish mainDish;
    private String establishment;
    private final int michelinStars;

    public RestaurantChef(String name, MainDish mainDish, String establishmentType,int michelinStars){
        this.name = name;
        this.mainDish = mainDish;
        this.establishment=establishmentType;
        this.michelinStars=michelinStars;
    }

    public void setChefsMainDish(MainDish mainDish) {
        this.mainDish=mainDish;
    }

    public MainDish getChefsMainDish() {
        return mainDish;
    }

    public String getEstablishmentType() {
        return establishment;
    }

    public void setEstablishmentType(String typeOfEstablishment) {
        this.establishment=typeOfEstablishment;
    }
} 
```

然后我们有了`FoodEstablishment`类:

```
//Can be a restaurant or a food truck
public class FoodEstablishment {
    private final List<Chef> chefs;
    private final int lotation;
    private final String type;

    public FoodEstablishment(List<Chef> chefs, int lotation, String type){
        this.chefs = chefs;
        this.lotation = lotation;
        this.type = type;
    }

    public int getLotation() {
        return lotation;
    }

    public List<Chef> getChefs() {
        return chefs;
    }

    public String getType() {
        return type;
    }
} 
```

和`Chef`接口:

```
public interface Chef {
     void setChefsMainDish(MainDish mainDish);
     MainDish getChefsMainDish();
     String getEstablishmentType();
     void setEstablishmentType(String typeOfEstablishment);
} 
```

让我们从这个小领域开始。

我们有一个由`RestaurantChef`和`FoodtruckOwner`实现的`Cook`接口，因为他们都有自己擅长的主菜，并且他们都确切地知道他们拥有的机构的类型(咄，:D)。

我们还有一个表示`FoodEstablishment`的更一般的类，当然，它可以是餐馆或食品车。

为了理解 Jackson ObjectMapper 及其内部工作方式，请注意我们在`FoodEstablishment`类中有一个`chefs`属性，它属于“T2 接口列表”类型，因此是一个由接口参数化的集合。稍后这将是很重要的，那时我们将关注**反序列化**。

**深入:使用 Jackson 进行序列化和反序列化**

既然我们介绍了学习杰克逊的小领域，让我们深入一些细节。

*序列化*

序列化是将 Java 对象编写成 JSON 格式的过程(在 web 上可以找到对 POJOs(普通 Java 对象)的引用),但是基本上，您只需要一个您想要在域中序列化的类来遵循一些属性，任何东西都可以被序列化。

让我们来看一个小单元测试，将`FoodtruckOwner`类:
序列化为 JSON 文件

```
class FoodtruckOwnerMapperTest {

    ObjectMapper mapper = new ObjectMapper();
    @Test
    public void serialization() throws IOException {
        FoodtruckOwner owner = new FoodtruckOwner("John", new MainDish("rice",6.7),"foodTruck");
        File jsonOwner = new File("/home/bruno/IdeaProjects/objectMapperJackson/owner.json");
        mapper.writeValue(jsonOwner, owner);
    }
} 
```

为了了解序列化是如何工作的，首先创建我们想要序列化的类的一个实例，并且创建一个文件来存储序列化的版本。

然后，我们调用 mapper 类的`writeValue`方法，该方法可以抛出一个异常，我们将该异常设置为在测试方法本身中抛出，这样我们就可以看到什么时候失败以及失败的原因。映射器可能会引发异常，因为序列化有时可能会失败。

如果我们运行测试，它会通过，如果我们检查`owner.json`文件的内容，我们会看到:

```
{"chefsMainDish":{"name":"rice","calories":6.7},"establishmentType":"foodTruck"} 
```

您现在知道如何将一个类从 Java 序列化为 JSON 了！

然而，一些重要的观点:

*   厨师的名字是“John ”,但在 JSON 文件中没有。
*   JSON 文件中的“键”是根据类中 getter 方法的名称设置的。

**注释和对象映射器要求**

厨师的名字不在序列化版本中，**，因为我们没有属性**的 getter。因此，为了能够序列化属性，我们需要为想要序列化的属性设置 getters。如果我们将缺失的 getter 添加到类中，即使有一个更具体的名字:

```
 public String getFoodtruckOwnerName() {
        return name;
    } 
```

那么我们的连载版本是:

```
{"chefsMainDish":{"name":"rice","calories":6.7},"establishmentType":"foodTruck","foodtruckOwnerName":"John"} 
```

因此，关于序列化，我们了解到:

*   我们需要为想要序列化的属性设置 getters。
*   getter 的实际方法名在 JSON 中用作标识属性的键。

**使用注释来配置序列化**

我们可以使用注释来配置序列化。例如，“foodtruckOwnerName”看起来很冗长，也许我们只想用关键字“Name”序列化属性。

为此，我们可以在属性的 getter 方法上使用注释`@JsonProperty` **，该属性接收序列化的新名称作为参数，比如:** 

```
@JsonProperty("nameOfCook")
    public String getFoodtruckOwnerName() {
        return name;
    } 
```

我们得到下面的 JSON:

```
{"chefsMainDish":{"name":"rice","calories":6.7},"establishmentType":"foodTruck","nameOfCook":"John"} 
```

我们感兴趣的最后一个属性是，例如，不序列化具有空值的字段，比如说，我们收到一个类实例，其中有人忘记设置厨师的名字，所以它是空的。我们得到这个 JSON:

```
{"chefsMainDish":{"name":"rice","calories":6.7},"establishmentType":"foodTruck","nameOfCook":null} 
```

如果我们不想在 JSON 中包含空值字段，我们可以做两件事:

1.  在类级别使用`@JsonInclude(JsonInclude.Include.NON_NULL)`注释，这将忽略类中所有具有空值的字段。
2.  在字段级别使用相同的注释，这将只忽略特定的空值字段。

对于本教程，这将是它关于**序列化**。在**上反序列化**。

*反序列化*

**与 Jackson 的反序列化**

既然我们已经介绍了序列化的基础知识，是时候进入相反的过程了，即**反序列化**。

当反序列化时，我们接收一个 JSON 文件作为输入，并将它映射到我们想要反序列化到的相应类的 Java 对象中。使用映射器，它看起来是这样的:

```
@Test
    void deserialization() throws IOException {
        FoodtruckOwner owner = new FoodtruckOwner("John", new MainDish("rice",6.7),"foodTruck");
        File jsonOwner = new File("/home/bruno/IdeaProjects/objectMapperJackson/owner.json");
        mapper.writeValue(jsonOwner, owner);
        FoodtruckOwner chef = mapper.readValue(new File("/home/bruno/IdeaProjects/objectMapperJackson/owner.json"),
                FoodtruckOwner.class);
    } 
```

因此，我们像上面一样进行序列化，并尝试将其从 JSON 反序列化回我们的域模型类`FoodtruckOwner`。然而，我们得到这个错误:

```
com.fasterxml.jackson.databind.exc.InvalidDefinitionException: Cannot construct instance of `objectmapper.domain.FoodtruckOwner` (no Creators, like default construct, exist): cannot deserialize from Object value (no delegate- or property-based Creator)
 at [Source: (File); line: 1, column: 2] 
```

这有些出乎意料。在成功地将类序列化为 JSON 之后，我们在将它读回 Java 时遇到了无法预料的麻烦。

Jackson 要求在目标类上有一个空的默认构造函数，所以，如果我们现在有了这个:

```
 FoodtruckOwner(){

    }

    public FoodtruckOwner(String name, MainDish mainDish, String establishmentType){
        this.name = name;
        this.mainDish = mainDish;
        this.establishment=establishmentType;
    } 
```

它肯定会工作，对不对？

不完全是，事实证明我们在`MainDish`类中有相同的缺省构造函数...这开始变得复杂了。让我们将默认构造函数添加到我们的`MainDish`类中。

在将默认的构造函数添加到`MainDish`类之后，我们得到了我们想要的，测试是绿色的，并且我们在 Java 中有了我们的实例，被正确地反序列化:

```
@Test
    void deserialization() throws IOException {
        FoodtruckOwner owner = new FoodtruckOwner("John", new MainDish("rice",6.7),"foodTruck");
        File jsonOwner = new File("/home/bruno/IdeaProjects/objectMapperJackson/owner.json");
        mapper.writeValue(jsonOwner, owner);
        FoodtruckOwner chef = mapper.readValue(new File("/home/bruno/IdeaProjects/objectMapperJackson/owner.json"),
                FoodtruckOwner.class);
        assertEquals("John", chef.getName());
        final MainDish chefsMainDish = chef.getChefsMainDish();
        assertNotNull(chefsMainDish);
        assertEquals("rice", chefsMainDish.getName());
        assertEquals(6.7, chefsMainDish.getCalories());

    } 
```

因此，我们看到，**在添加了默认构造函数**之后，我们的反序列化过程成功了，我们成功地将代表食品卡车车主的 JSON 正确地读入 Java。然而，有一些问题。

有必要以相当侵入性的方式修改 Java 类，因为我们需要显式地向这些类添加默认构造函数，从反序列化过程的角度来看，这些类只是“客户端”。这可不好。如果`MainDish`类实际上只是一个我们不能直接访问的库，那会怎么样？那么源代码中的这些修改即使不是完全不可能，也是不切实际的(不是不可能，像 IntelliJ 中的“asm inspector”这样的工具可以有效地将库反编译成源代码，您可以复制并适应您的需求，但是，这不是办法)。

让我们假设这样的场景是我们所面临的:我们不能修改`MainDish`的源代码。那怎么接近呢？

**杰克逊·米欣斯**

当我们不能直接修改源代码以遵守 Jackson 强加的约定时，我们需要求助于一些特殊的功能，允许 Jackson 与第三方代码“间接”交互。
其中一个功能叫做[“混音”](https://github.com/FasterXML/jackson-docs/wiki/JacksonMixInAnnotations)。

“混合”注释是一种将注释与类相关联的方法，无需修改(目标)类本身，最初旨在帮助支持第三方数据类型，其中用户不能修改源来添加注释。

使用混音，您可以:

*   定义''' mix-in 类' ' '(或接口)注释将与' ' '目标类' ' '(或接口)一起使用，这样看起来好像“目标类”具有“mix-in”类所具有的所有注释(为了配置序列化/反序列化),您可以将其视为一种在运行时添加更多注释的面向方面的方式，以增加静态定义的注释。

这正是我们的案例所需要的，因为我们无法修改`MainDish`类。

我们可以使用下面的混音:

```
public abstract class MainDishMixin extends MainDish {
    @JsonCreator
    public MainDishMixin(@JsonProperty("name") String name, @JsonProperty("calories") double calories) {
        super(name, calories);
    }
} 
```

这里我们注意一些重要的事情:

*   mix-in 必须在不同于目标类的包中定义，并且它**不能被定义为目标**的内部类

*   模拟类的原始构造函数的参数用`@JsonProperty`注释进行了注释，并使用了与目标类中相同的名称**和类型**

然后我们使用`mapper.addMixIn(MainDish.class, MainDishMixin.class);`在我们的 ObjectMapper 类中注册 MixIn，我们就完成了！

那么测试将会通过，我们达到了我们的目的:在没有直接修改目标类的情况下，我们成功地将它反序列化为一个 Java 对象。

**反序列化类中的接口字段**

我们的最后一个特性是如何反序列化在我们的代码中被参数化为接口的字段。让我们假设我们将所有的 getters 和 setters 添加到了类`RestaurantChef`和`FoodtruckOwner`中，序列化它，现在我们已经有了这个 JSON:

```
{"chefs":[{"name":"Michel","michelinStars":4,"chefsMainDish":{"name":"Chicken","calories":20.0},"establishmentType":"Restaurant"},{"name":"John","chefsMainDish":{"name":"rice","calories":6.7},"establishmentType":"foodTruck"}],"lotation":10} 
```

让我们假设对于最后一个例子，我们有这个类:

```
//Can be a restaurant or a food truck
public class FoodEstablishment {
    private List<Chef> chefs;
    private int lotation;

    public FoodEstablishment(){}

    public FoodEstablishment(List<Chef> chefs, int lotation){
        this.chefs = chefs;
        this.lotation = lotation;
    }

    public int getLotation() {
        return lotation;
    }

    public List<Chef> getChefs() {
        return chefs;
    }
} 
```

不幸的是，试图按原样反序列化这个类会给我们带来另一个错误:

```
DefinitionException: Cannot construct instance of `objectmapper.domain.Chef` (no Creators, like default construct, exist): abstract types either need to be mapped to concrete types, have custom deserializer, or contain additional type information
 at [Source: (File); line: 1, column: 11] (through reference chain: objectmapper.domain.FoodEstablishment["chefs"]->java.util.ArrayList[0]) 
```

这意味着，由于`Chef`在那里被用作接口，Jackson 不会知道如何反序列化它，因为该接口实际上由两个实现共享。为了完成这项工作，我们需要用关于不同子类型的附加信息来注释接口:

```
@JsonTypeInfo(
        use = JsonTypeInfo.Id.CLASS,
        include = JsonTypeInfo.As.PROPERTY,
        property = "class")
@JsonSubTypes({
        @JsonSubTypes.Type(value = RestaurantChef.class, name = "restaurantChef"),
        @JsonSubTypes.Type(value = FoodtruckOwner.class, name = "foodtruckOwner")})
     public interface Chef {
     void setChefsMainDish(MainDish mainDish);
     MainDish getChefsMainDish();
     String getEstablishmentType();
     void setEstablishmentType(String typeOfEstablishment);
} 
```

添加了上面的注释之后，它提供的信息，让 Jackson 在反序列化的时候可以推导出要使用哪个实现，一切都可以了！

**结论**

我希望这篇文章很好地全面介绍了使用 Jackson 时 Java 中的序列化和反序列化过程，并允许从这里开始进行更多的探索。感谢阅读！