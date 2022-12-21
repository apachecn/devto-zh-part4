# 带动作的嵌套构建器

> 原文：<https://dev.to/greenfieldcoder/nested-builders-with-action-t-18dj>

一旦我开始使用构建器模式，没多久我就在多层次上面对更复杂的对象。

我的第一个解决方案是添加一个返回新构建器的方法，在这个构建器中有一个完整的方法可以返回我原来的构建器。

听起来很复杂，不是吗？确实是！一旦我超越了两层嵌套，构建器的实现每次都是一个真正的地狱。

然后我发现我可以使用 Action，其中 T 是另一个构建器。这使得我的构建器更容易实现，也更容易维护。

下面是一个代码示例，展示了它的样子:

```
public class Person
{
    public string Firstname { get; set; }
    public string Lastname { get; set; }
    public List<Person> Children { get; set }
}

public class PersonBuilder 
{
    private readonly Person _person;

    public PersonBuilder()
    {
        _person = new Person();
    }

    public PersonBuilder Firstname(string firstname)
    {
        _person.Firstname = firstname;

        return this;
    }

    public PersonBuilder Lastname(string lastname)
    {
        _person.Lastname = lastname;

        return this;
    }

    public PersonBuilder AddChild(Action<PersonBuilder> builder)
    {
        var personBuilder = new PersonBuilder();
        builder(personBuilder);

        _person.Children.Add(personBuilder.Build())

        return this;
    }

    public Person Build()
    {
        return _person;
    }
}

var person = new PersonBuilder()
                        .Firstname("John")
                        .Lastname("Doe")
                        .AddChild(child => {
                               child.Firstname("Jane")
                                    .Lastname("Doe")
                        })
                        .Build(); 
```