# 单元测试:模拟

> 原文：<https://dev.to/ice_lenor/unit-testing-mocks-2ki3>

*这篇文章最初发表在我的博客[smartpuffin.com](https://smartpuffin.com/unit-tests-mocks/)上。*

* * *

在本教程中，我们学习如何在单元测试中使用模拟，为什么集成测试有时是不够的，以及如何拯救一些树。🌳

# 先决条件

我假设您已经熟悉了单元测试的一些基本概念。我将使用 Java、JUnit 和 Mockito，但是如果您不熟悉它们也不用担心:本教程的目的是学习使用 mock。您将在运行中获得特定于平台的东西。

这里有一个到库的链接，我在这里放了这篇文章的全部源代码。可以下载下来边看边看，也可以事后再看。

我们走吧！

# 我们的项目

我从一个朋友那里借用了这个想法。她的儿子当时 5 岁，每天都会问她:“妈妈，妈妈，我今天活了多少天？”

妈妈会计算并告诉他:“亲爱的，你今天 1868 天了”。

第二天，儿子又会问。

所以她给他写了一个 iOS 应用，他可以每天打开，自己看。

我认为这可能是一个不错的想法，本教程的基础:)。

当然，我们今天不打算写一个完整的 iOS 应用。我们的项目将是一个简单的 Java 控制台应用程序。我们将有几个注册用户的小数据库。当用户请求他们的年龄时，我们将告诉他们。

我将省略我们的应用程序中的许多功能:将没有用户注册过程；而我们的数据库是一个小小的`.csv`文件；而且没有花哨的用户界面，只是打印到控制台。我尽可能简单地举了这个例子来说明嘲讽的技巧。

这里是[库](https://github.com/ice-lenor/UnitTestsMocks)，所有的源代码都在这里。

我们的主要逻辑位于这个文件中，[UserAgeCalculator.java](https://github.com/ice-lenor/UnitTestsMocks/blob/master/src/main/java/user_age/UserAgeCalculator.java)。它包含的方法`calculateUserAge`将成为我们今天关注的焦点。

```
// Calculates the user age in days.
public int calculateUserAge(int userId, Date dateTo) {
    User user = usersDatabase.getUserById(userId);
    if (user == null)
        throw new IllegalArgumentException("User doesn't exist");

    double daysDateTo = dateTo.getTime() / MILLISECONDS_IN_DAY;
    double daysUserBirthDate = user.getBirthDate().getTime() / MILLISECONDS_IN_DAY;
    return (int)(daysDateTo - daysUserBirthDate);
}

```

这段代码非常简单。首先，我们根据 userId 从数据库中获取用户。如果没有找到用户，我们抛出一个异常，因为我们无法计算我们不认识的人的年龄。

之后，我们计算提供的日期和用户出生日期之间的天数差。

如果我们看一下[我们的数据库](https://github.com/ice-lenor/UnitTestsMocks/blob/master/resources/users_database.csv)，它是这样的:

```
1,"Emma","1983-12-03"
2,"Janneke","1992-04-25"
3,"Jaap","1996-02-19"
4,"Sophie","1978-07-30"
5,"Pieter","2019-01-01"

```

我们注册了几个用户，都有用户 id、他们的名字和出生日期。

And now, to our first test.

# 首次测试

这是我们的第一次测试。

```
@test

public void userEmmaAge() {
    UsersDatabase usersDatabase = new UsersDatabase();
    UserAgeCalculator calculator = new UserAgeCalculator(usersDatabase);
    Date dateTo = DateHelpers.parseDate("2019-01-01");

    int resultAge = calculator.calculateUserAge(1, dateTo);

    assertEquals(12813, resultAge);
}

```

我们选择了 id=1 的用户 Emma，并计算了 2019 年 1 月 1 日和 Emma 生日(1983 年 12 月 3 日)之间的天数差。

艾玛已经 12813 天了。

## 整合测试？

我们刚刚写的叫做集成测试。

我们不仅测试我们的`calculateUserAge`方法。我们正在一起测试几段代码。换句话说，我们的`calculateUserAge` *依赖*其他代码才能工作，我们在测试中保持了这种依赖性。

这取决于什么？

实际上，这取决于`UsersDatabase`的实现。`calculateUserAge`正在调用`UsersDatabase.getUserById`方法，该方法打开数据库，通过 id 读取用户，并将该用户返回给调用者。

因此，简而言之，我们的单元测试依赖于数据库。

## 为什么这样不好？

这不一定是坏事。

集成测试是一个很好的工具，可以确保你的整个应用程序能够很好地协同工作。当你有几段代码，被单元测试覆盖得很好，但是你没有一起测试它们，有可能你错过了一个 bug。这些部分一起工作可能会有问题，如果你只单独测试它们，你可能会错过它。

如果我们在这里开发一个真正的应用程序，我们可以决定进行一系列的单元测试来检查计算逻辑和一些集成测试来确保整个应用程序运行良好。

集成测试的运行成本通常更高。当您的测试访问数据库、将一些数据推入队列、通过网络调用另一个服务并处理响应数据时，这需要时间。集成测试比单元测试运行得慢。

除此之外，您可能需要一些保护措施来确保您的集成测试不会与真实的生产数据混淆。

## 为什么使用生产数据不好？

还是那句话，本身不一定不好。然而，这增加了一些复杂性。

### 测试数据库中的写入

如果您的测试将一些数据写入数据库，就会出现一个问题。如果您运行此测试，数据库中将会有新条目。您必须采取措施确保不在生产数据库上运行这个。

### 测试从数据库读取的数据

读取数据要简单一些。在读取数据的时候，你不要乱动，你只是读取数据，对吗？你的产品是安全的，对吗？

虽然这可能是真的，但阅读仍然存在一些问题。

## 失踪案件

我们可以列举几个集成测试的问题，这些问题让我们无法全面深入地测试我们的应用程序。

### 数据稳定性

一个问题是生产数据有时**不稳定**。在我们的年龄计算应用程序中，每天都有新用户注册，也有一些用户离开应用程序。

如果艾玛删除了她的账户会怎么样？

您可以通过从我们的小数据库中删除 Emma 并运行测试来模拟这种情况。

是的，我们的测试将开始失败。由于找不到 id=1 的用户，`calculateUserAge`方法会抛出异常，这肯定不是我们所期望的。

### 数据丰富度

我们新应用的生产数据很少。许多病例在我们的数据库中还没有(目前还没有)。

如果我们考虑案例，就像我们在本文中讨论的[，我们可能会发现我们想要测试的不仅仅是我们 5 个用户提供的服务。](http://smartpuffin.com/unit-tests-tutorial/)

例如，现在我们还没有一个未来将要出生的用户。但是，根据医生的预测，一个怀孕的父母可能会在我们的应用程序中注册，并计算孩子出生前的天数。我想测试一下这种情况，以确保在生产中发生这种情况时，我们的应用程序已准备好并显示正确的数字:即-10 天。

另一种情况是，当我们想要计算数据库中不存在的用户的年龄时。记住，我们的`calculateUserAge`方法在这种情况下抛出一个异常。

为此，我们需要在测试中获取一个 id 不在数据库中的用户。比方说，我们现在有 5 个用户，id 从 1 到 5，我们对一个不存在的用户使用 42 怎么样？

```
@test

public void nonExistingUser() {
    UsersDatabase usersDatabase = new UsersDatabase();
    UserAgeCalculator calculator = new UserAgeCalculator(usersDatabase);

    Date dateTo = DateHelpers.parseDate("2019-09-12");

    assertThrows(IllegalArgumentException.class, () -> {
        int resultAge = calculator.calculateUserAge(42, dateTo);
    });
}

```

当然，如果我们的应用程序很受欢迎，我们很快就会有 42 个用户。这个测试将开始失败，因为它不再抛出异常。

### 环境稳定性

当与数据库的连接失败时，我们可能希望向用户显示一条消息:“对不起，出错了，请稍后再试”。

很自然，我们可能想测试一下。

然而，有可能我们的连接非常稳定，并且在您运行测试时数据库不会关闭。

这意味着预计数据库会失败的测试很可能永远不会通过。

想象一下，一个不同的应用程序编译一份报告并将其发送到打印机。你不会想每次做测试都浪费纸张吧？在这种情况下，“切断”真实环境有助于简化应用程序并专注于其逻辑，还可以节省一些树。🌳

# 嘲弄的

为了克服这些问题，我们将把这个测试变成一个真正的单元测试。我们将切断对数据库的依赖，并用我们自己的“数据库”取而代之，一个假的数据库，我们将为每次测试单独制作的数据库。

代码将不会知道数据库是假的，并将正常工作。但是我们会的！这让我们变得强大。

让我们看看它是怎么做的。

看看这个[新的测试套件](https://github.com/ice-lenor/UnitTestsMocks/blob/master/src/test/java/user_age/UserAgeCalculatorTest2.java)。

```
@test

public void userBabyAge() {
    UsersDatabase usersDatabase = Mockito.mock(UsersDatabase.class);
    int userId = 42;
    User user = new User(userId, "Baby", DateHelpers.parseDate("2019-09-01"));
    Mockito.when(usersDatabase.getUserById(userId)).thenReturn(user);

    Date dateTo = DateHelpers.parseDate("2019-09-12");

    UserAgeCalculator calculator = new UserAgeCalculator(usersDatabase);

    int resultAge = calculator.calculateUserAge(userId, dateTo);

    assertEquals(11, resultAge);
}

```

看看我们如何创建`UsersDatabase`实例。这是我们之前做的:

```
UsersDatabase usersDatabase = new UsersDatabase();

```

我们现在正在做的是:

```
UsersDatabase usersDatabase = Mockito.mock(UsersDatabase.class);

```

我们刚刚创建了一个虚拟的数据库，一个完全虚假的数据库，它将按照我们的意愿运行，并且只为我们的测试而存在。

之后，我们创建一个用户:

```
User user = new User(userId, "Baby", DateHelpers.parseDate("2019-09-01"));

```

我们刚刚创建了一个数据库中不存在的用户。一个完全假装的用户，不是真实的。

然后，我们让我们的虚拟数据库与我们的虚拟用户一起工作:

```
Mockito.when(usersDatabase.getUserById(userId)).thenReturn(user);

```

之后，我们创建计算器并用`userId = 42`计算这个用户的年龄。

看到我们在做什么了吗？只有这个假想的用户“存在”在我们假想的“数据库”中，我们只为这个测试制作了它们。现在测试还没有进入真正的`UsersDatabase`。我们拥有所有的力量！

这种技术叫做*模仿*，这个假数据库叫做*模仿*。通过添加这个模拟，并用它替换真实的数据库，我们已经使我们的测试成为一个纯粹的单元测试。这真的很强大，因为现在我们可以测试以前无法测试的所有场景。

非现有用户怎么样？来自未来的用户怎么样？现在一切皆有可能。

## 模仿还有哪些用途？

假设我们不会将用户的年龄作为返回值返回，而是将它打印在纸上。

这很难测试。当然，您不希望浪费纸张，但更大的问题是，您的测试必须找到打印机，从打印机中取出纸张，对文本进行拍照，应用一些很酷的文本识别技术来查看正在打印的内容，并将识别的文本与预期值进行比较。

这听起来很酷，但是实现起来很困难。有没有更简单的方法？

是的，嘲笑也有助于此。如果您要模拟打印机实例，您可以确保用特定的参数调用一次`Printer.print()`。

模仿框架有许多有用的可能性。您可以[检查一个方法被调用了多少次](https://github.com/ice-lenor/UnitTestsMocks/blob/master/src/test/java/user_age/MockingCapabilities.java)。您可以看到每次传递了哪些参数，然后只比较需要的参数。您可以模拟对一个方法的多次连续调用，并使它们返回不同的值。您可以让每第二个调用抛出一个异常。我不确定这是否有用，但是，嘿，可能有用。看看你最喜欢的嘲讽框架的文档，看看完整的列表。

## 摘要

我们已经简要讨论了单元测试和集成测试之间的区别。有时，单元测试比集成测试更可取。与此同时，编写、支持和运行单元测试比集成测试更容易、更便宜。

我们已经学会了应用模仿来将我们的代码从外部依赖中隔离出来，并编写纯单元测试。

嘲讽让我们探索更多使用“真实”数据时不可能的场景，并且我们的测试更加稳定，因为我们现在控制了环境。