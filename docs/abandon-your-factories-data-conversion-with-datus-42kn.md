# 放弃你的工厂-数据转换与 datus

> 原文：<https://dev.to/roookeee/abandon-your-factories-data-conversion-with-datus-42kn>

(声明:我是 [datus](https://github.com/roookeee/datus) 的作者)

你再次使用 Java，也许你正在开发一个新的(微)面向服务的架构，并且正在将另一个服务`PersonService`集成到你的`AService`中。您收到了一个`PersonResource`，正如您所了解的，您将该对象转换成您自己的`Person`对象，以便不依赖于您的业务逻辑中不拥有的数据结构:

```
//external object
class PersonResource {
    //getters + setters omitted for brevity
    private String firstName;
    private String lastName;
}
//internal object
class Person {
    //getters + setters omitted for brevity
    private String firstName;
    private String lastName;
} 
```

让我们构建这个方便的工厂/转换器对象:

```
public PersonFactory {
    public Person create(PersonResource resource) {
      Person person = new Person();
      person.setFirstName(resource.getFirstName());
      person.setLastName(resource.setLastName());
      return person;
    }
} 
```

这是可行的，但是其他 5-10 服务和它们的 15-30 数据对象呢？所有这些锅炉板都很烦人，不是吗？刚才你意识到一个`PersonResource`可以为空，所以你的测试覆盖工具抱怨没有为这样一个微不足道的类编写测试。

如果有一种方法来声明转换是如何完成的，定义它是如何完成的，但不实现它，会怎么样？输入 [datus](https://github.com/roookeee/datus) :

```
Mapper<PersonResource, Person> mapper = Datus.forTypes(PersonResource.class, Person.class)
    .mutable(Person::new)
    .from(PersonResource::getFirstName).into(Person::setFirstName)
    .from(PersonResource::getLastName).into(Person::setLastName)
    .build(); 
```

非常简洁，不言自明——声明式方法目前看起来不错。但是我刚才说的那张空头支票呢？让我们实现它，但是如何实现呢？

```
Mapper<PersonResource, Person> mapper = Datus.forTypes(PersonResource.class, Person.class)
    .mutable(Person::new)
    .from(PersonResource::getFirstName).into(Person::setFirstName)
    .from(PersonResource::getLastName).into(Person::setLastName)
    .build();

Mapper<PersonResource, Optional<Person>> notNullMapper = 
    mapper.predicateInput(Objects::nonNull) 
```

这很容易，但是项目在发展:`lastName`可以是空的，在您的服务中应该是大写的。你不会质疑这个奇怪的需求并添加新的功能:

```
Mapper<PersonResource, Person> mapper = Datus.forTypes(PersonResource.class, Person.class)
    .mutable(Person::new)
    .from(PersonResource::getFirstName).into(Person::setFirstName)
    .from(PersonResource::getLastName)
         .given(Objects::nonNull, ln -> ln.toUpperCase()).orElseNull()
         .into(Person::setLastName)
    .build();

Mapper<PersonResource, Optional<Person>> notNullMapper = 
    mapper.predicateInput(Objects::nonNull) 
```

想不到！但是您不希望任何没有`lastName`的`Person`对象有效，所以让我们为输出对象添加一个谓词(当然您已经可以在输入对象上这样做了，但是让我展示一些更多的特性；)):

```
Mapper<PersonResource, Person> mapper = Datus.forTypes(PersonResource.class, Person.class)
    .mutable(Person::new)
    .from(PersonResource::getFirstName).into(Person::setFirstName)
    .from(PersonResource::getLastName)
         .given(Objects::nonNull, ln -> ln.toUpperCase()).orElseNull()
         .into(Person::setLastName)
    .build();

Mapper<PersonResource, Optional<Person>> predicatedMapper = 
    mapper.predicate(Objects::nonNull, p -> p.getLastName() != null) 
```

那么我们得到了什么？

*   声明你的映射，不要实现它们
*   对于这种琐碎的空性检查，不需要单元测试:它们被声明，一旦需要它们，就简单地添加(这是一个观点)
*   不再有工厂类，只有一个接口
*   **datus 甚至适用于不可变的目标对象(查看 readme 示例)**
*   没有使用反射，所有东西都是静态类型检查的

哦，我有没有提到`Mapper<A,B>`也提供了`List<B> convert(List<A> input)`和`Map<A,B> convertToMap(List<A> input)`开箱即用？在[自述文件](https://github.com/roookeee/datus/blob/master/README.md)或[扩展用户指南](https://github.com/roookeee/datus/blob/master/USAGE.md)中找到更多信息，阅读该指南只需 15 分钟！

你喜欢自己一个更隐式/自动/注释驱动的转换过程吗？检查 datus 的替代方案，如 [MapStruct](http://mapstruct.org/) 或 [ModelMapper](http://modelmapper.org/) 。

谢谢你的时间，如果你有任何问题打电话给我:)