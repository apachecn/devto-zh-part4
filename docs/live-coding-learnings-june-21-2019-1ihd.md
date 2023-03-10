# 实时编码学习-2019 年 6 月 21 日

> 原文：<https://dev.to/jitterted/live-coding-learnings-june-21-2019-1ihd>

# Quizdown:第 20 集

Quizdown 是一个基于 Java+Spring 的应用程序，它使用类似 Markdown 的语法来创建编码测验。

## 故事实现了

经过一次重要的重构(参见下面的**无裸数据结构**),现在
我有能力在问题之间来回移动。如果先前选择了该选项，HTML 生成现在会生成`checked`。下一步是从数据库加载用户的响应，并将其发送到 UI 控制器。

## HTML:对按钮或链接使用禁用属性

当我在寻找合适的 CSS 类来显示一个被禁用的按钮时，我发现 HTML 不使用`is-disabled` CSS 类，而是支持`disabled`作为`button`和链接(`<a>`)的属性。例如，将链接显示为*禁用*，而不是:

```
<a class="is-disabled" href="/question/0">Previous Question</a> 
```

您可以像这样使用`disabled`属性:

```
<a href="/question/0" disabled>Previous Question</a> 
```

## 没有裸数据结构

我的一个 Java 设计启发是*无裸数据结构*，这意味着包装原始数据结构(`List`、`Map`等)。)放在一个类型中，这样您就可以传递它，并以一种从领域角度来看有意义的方式与之交互。

> 这类似于*无字符串类型代码*启发式:不要到处使用`String`，而是使用类和接口类型。详情见此处的[和此处](http://wiki.c2.com/?StringlyTyped)的[。](https://kamilszymanski.github.io/refactoring-stringly-typed-systems/)

当添加允许用户来回浏览问题的特性时，我需要让用户做出的选择可用。以前，我将这些选择存储为一个`Set<String>`，因为它很简单，并且没有在很多地方使用。然而，一旦我在更多的地方需要它(把它从数据库推回到前端)，传递一个“裸的”`Set`会使代码更难阅读和使用。例如，对于`Set`，判断它是否正确取决于问题的类型(`FIB`是填空题，`MC`是选择题):

```
 public boolean isCorrectFor(@NonNull Set<String> response) {
    return switch (questionType) {
      case FIB -> correctChoices.containsAll(response);
      case MC -> correctChoices.equals(response);
    };
  } 
```

这是非常典型的代码，但是除非您熟悉`Set`的细节，否则您可能需要阅读一些 JavaDoc 来确保您正确理解它。通过用一个*封装*和`Set`的`Response`类型替换它，上面的代码变成了:

```
 public boolean isCorrectFor(Response response) {
    return switch (questionType) {
      case FIB -> response.matchesAny(correctResponse);
      case MC -> response.allMatch(correctResponse);
    };
  } 
```

现在，这可能不是一个惊人的改进(我仍然在摆弄命名)，但它导致了下一步(我还没有完成)将特定于问题类型的行为推入响应的子类，例如，`FibResponse`和`McResponse`，看起来像:

```
 public boolean isCorrectFor(Response response) {
    return response.correctlyMatches(correctResponse);
  } 
```

将如何比较响应完全委托给最知道如何进行比较的班级。

在我对 Java 开发人员的培训和指导中，当事情发生变化时，字符串类型和裸数据结构的使用是许多痛苦的原因，所以总是尝试将字符串封装到值对象中，并将数据结构包装到它自己的类中。

## 曝光`asSet()`回应:破解封装？

通过将`Set<String>`封装在`Response`中，意味着当我需要显示响应选择或将它们保存到数据库中时(例如，跨越*域边界*提交响应)，我需要将这些选择作为一个集合，因此`Response`类有一个方法

```
public Set<String> asSet() {
  return Set.copyOf(response); // return a copy of our internal field
} 
```

它将选择作为一个`Set<String>`返回。我在`GradedAnswerView`类中将它转换成逗号分隔的`String`:

```
 private static String responseAsString(Answer answer) {
    return answer.response().asSet()
                 .stream()
                 .sorted()
                 .collect(Collectors.joining(", "));
  } 
```

您可能会想:*哦，不，这是通过暴露集合*来破坏封装，但是我并没有真的这么做，因为我正在返回响应选择的内部存储的副本。我从不公开字段本身，所以修改内部`Response`数据的唯一方法是通过它的方法。修改副本不会有任何效果。

## 空 varargs

我想我以前没有意识到这一点，但是调用一个用 varargs(例如，`createFrom(String... strings)`)定义的方法意味着你可以用*没有*参数:`createFrom()`调用那个方法，在这个方法中，`strings`参数将是一个空数组。

这意味着我不必将 varargs 转化为空集，因为不带参数调用`Set.of()`将转化为空集(从技术上讲，从 Java 9 开始就是一个`ImmutableCollections.emptySet()`)，这正是我想要的。

## 协同测试

我的大多数测试都是单元测试，因为他们通过尽可能紧密地与类一起工作来测试类中的代码。比如 [`Choice`级](https://github.com/jitterted/quizdown/blob/7b9c120604fa3957e464b0f965fef1ab339de1fe/src/main/java/com/jitterted/quizdown/adapter/web/MultipleChoiceTransformer.java#L45)可以直接测试，不需要任何其他对象。然而，`QuestionTransformer`不能单独测试，因为它的工作主要是与更具体的转换器协作来完成它的工作，也就是说，它的工作是与其他对象集成或协作。这意味着我不需要全面测试所有相关类的所有行为，只需要一两个测试来确保`QuestionTransformer`正确地*与其他对象协作(在本例中，传播响应给其他对象)。*

你可以从我的视频中看到一个片段，我是这样做的:[https://www.twitch.tv/videos/443315061](https://www.twitch.tv/videos/443315061)。

* * *

请务必收听我的下一个现场编码流:[https://twitch.tv/jitterted](https://twitch.tv/jitterted)。

想支持我？成为 https://patreon.com/jitterted 的赞助人