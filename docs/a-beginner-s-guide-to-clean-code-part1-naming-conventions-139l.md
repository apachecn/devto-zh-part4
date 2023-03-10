# 简明代码指南:命名约定

> 原文：<https://dev.to/danialmalik/a-beginner-s-guide-to-clean-code-part1-naming-conventions-139l>

嗨伙计们，

最近我开始阅读罗伯特·c·马丁的《干净的代码》。到目前为止，我认为这是一本很棒的书，每个程序员都应该至少阅读一次，或者至少阅读开始的几章，因为在那些章节中，作者试图强调不应该被忽视的非常基础和基本的编码实践。

在这篇文章中，我将试着用我自己的话来总结这本书的第二章，并尽量让事情变得简单。所以我们开始吧:)

# 问题

“命名”是任何过程中非常重要的一步。我们花了很多心思给孩子取名，因为我们知道它的重要性。但是当我们对代码做同样的事情时，我们就没有足够的时间了。我们要么认为我们已经知道了最好的命名方法，要么认为这不值得我们浪费时间。糟糕的变量名在开始时可能看起来不错，但是当代码库随着更多开发人员的工作而变得越来越大时，管理和协调我们的工作就会变得非常困难。我们的代码是我们的创造，因此，我们至少应该给它起个合适的名字。

# 做什么？

### 变量的意图从它的名字就应该清楚。

*   如果你必须写一个注释来说明变量的用途，那么重新考虑这个名字。
*   在过去，我们有更小(非常小)的 ram，因此在给变量取大名字时必须非常仔细地考虑。但现在这已经不是问题了，不是吗？。那么为什么要给变量起模糊的名字，比如`a`、`n1`等等。

考虑这段代码

```
 getData(plist):
        ep = [] // expired person accounts
        for p in plist:
            if p.expiry_date >= datetime.now()
            ep.push(p)
        return ep 
```

Enter fullscreen mode Exit fullscreen mode

并与
进行比较

```
 get_expired_persons_list(persons_list):
        expired_persons_list = []
        for person in persons_list:
            if person.expiry_date >= datetime.now()
            expired_persons_list.push(person)
        return expired_persons_list 
```

Enter fullscreen mode Exit fullscreen mode

### 不要使用可能误导其他开发者的变量名。

*   一个例子是将一组`Person`对象命名为`persons_list`，而它实际上并不是一个`List`。这个名字可能会误导其他开发人员认为它是一个`List`类型的对象。因此将其命名为`persons_collection`将是一个更好的选择。

### 变量名应该是有意义的不同

*   我们经常倾向于用一种方式命名变量，这种方式只能消除由于相同的变量名而导致的编译器错误。一个例子是将变量命名为`PersonData`和`PersonData2`。在这个例子中，变量的用途不清楚。
*   类似地，如果你用像`get_person()`、`get_person_info()`这样的方法编写了一个`Person`类，其他开发人员就不容易调用正确的方法来达到预期的目的。开发人员很容易混淆这些方法的功能。

### 变量名应该是可发音的。

*   如果变量名是可发音的，我们的大脑可以很容易地记住它们，我们可以避免在以前的代码中搜索变量名以重用它的不必要的麻烦。例如，可以使用这样的名称:

```
class Person(object):
    ...
    get_age_year_month_days()
    get_last_modified_year_month_days()
    ... 
```

Enter fullscreen mode Exit fullscreen mode

而不是

```
class Model01(object):
    ...
    get_ageiymd() // get age in year, month, days
    get_lmdiymd() // get last modified in year, month, days
    ... 
```

Enter fullscreen mode Exit fullscreen mode

### 应该使用可搜索的变量和常量。

*   假设你有一个工作项目。要求每个用户至少年满 18 岁。您通过以下方式实现了这一点:

```
def create_person(data):
    if data['age']['year'] < 18:
        return None 
```

Enter fullscreen mode Exit fullscreen mode

*   类似地，你在其他需要的地方使用了这个数字`18`。

现在要求改变了，用户必须至少 15 岁才能创建帐户。你现在会怎么做？搜索数字`18`的每一次出现，并确定这是否是用于我们目的的数字，然后在任何地方更改它。

或者您可以简单地使用一个常量`MIN_AGE_REQ=18`或`MINIMUM_AGE_REQUIREMENT=18`(这更有意义和可搜索)。那么您必须简单地改变这个常量的值来满足新的需求。

### 类名应该永远是名词。

*   类名和对象应该总是像`Person`、`Car`、`Record`之类的名词。避免通用和特定语言的技术名称，如`Info`、`Manager`和`Controller`。

### 方法名应该有动词

*   方法名应该有动词或动词短语名(最好在开头)，比如`get`、`set`和`delete`。

### 为类似目的的名称确定一个标准。

*   假设您的项目中有许多类，许多开发人员都在为它们工作。您决定对 setters 和 getters 使用以下约定:

```
class PersonUpdateForm(object):
    ...
    get_age()
    set_age()
    ... 
```

Enter fullscreen mode Exit fullscreen mode

而另一个开发者可能会这样做:

```
class UpdatePersonForm(object):
    ...
    retrive_age()
    update_age()
    ... 
```

Enter fullscreen mode Exit fullscreen mode

这导致项目中的不一致性，并且难以搜索期望的对象和方法。在开始开发之前，决定你将遵循什么样的惯例，并让每个开发人员都遵循这些惯例。

### 在任何需要的地方使用带有变量名的上下文。

*   如果在类`MemberAddress`中有一个名为`state`的变量，那么很明显`state`属于一个`MemberAddress`对象。但是在类之外，如果使用了变量`state`，那么其他开发者可能不清楚变量`state`属于什么对象。在使用变量名的场景中，`member_address_state`会更有意义。

# 结论

我们永远不应该忽视命名实践的重要性，因为糟糕的变量名会成为未来开发计划中的一大障碍。此时更改代码库是不必要的开销，可以通过遵循良好的实践在一开始就避免。

目前这都是我这边的。我将很快写这本书的后面几章。

编码快乐！