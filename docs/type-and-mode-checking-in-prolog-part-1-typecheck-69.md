# prolog 中的类型和模式检查(第 1 部分):type_check

> 原文：<https://dev.to/terminusdb/type-and-mode-checking-in-prolog-part-1-typecheck-69>

说到用于规则处理的语言，prolog 无与伦比。它有如此多令人兴奋的功能，使其具有难以置信的灵活性，其中包括:

*   回溯:我们可以尝试几种策略来解决一个问题，如果其中一种不起作用，就回滚。这也给了我们使用相同框架的生成器和测试器，支持算法的猜测和检查方法。
*   同象性:这使得难以置信的元逻辑疯狂成为可能，包括术语和目标扩展以及简化元解释者的写作。
*   DCG(Declarative Clause Grammars):正则表达式很棒，但是当你的语言不规则时，DCG 给了我们一种最好的方法来解析输入。

然而，当试图出于工业目的愤怒地编写大型软件包时，有一些不幸的缺点往往会困扰这种原本美丽的语言。我已经整理了一个相当长的心理清单，但现在我想谈谈其中两个尴尬的元素以及如何处理它们。

当您在 prolog 中编写谓词时，您通常可以通过仔细选择术语，在谓词的子句之间做出非常有效的选择，这些术语使我们能够区分我们想要“激发”的子句。

```
p(term_a) :- write(term_a).
p(term_b) :- write(term_b). 
```

当我们调用 **p(term_a)** 时，我们被亲切地对待期望的打印输出。也许更好的是，使用 swipl 这样的 prolog，由于参数的索引，我们甚至不会有任何剩余的选择点，这也使我们的选择更有效。

然而，在大规模的项目中，可能会发生我们调用 **p** /1 时遇到了意想不到的事情，可能是 term_c，原因可能是打字错误，也可能是思想错误。prolog 处理这种情况的方法是悄悄地失败——因为 Prolog 中的失败是正常的，结果可能会导致恼人的 bug 搜索，需要相当长的时间才能将问题缩小到其根源。

当然，我们对 p /1 的期望是正确地输入它。也就是说，我们希望能够向 p 添加一些关于其参数域的附加信息，这样我们可以动态地，或者更好地，静态地，确定它们不是所需的种类。

## 辛德雷·米尔纳和式 _ 检查

输入 [Hindley-Milner](https://en.wikipedia.org/wiki/Hindley%E2%80%93Milner_type_system) (HM)，这是一个类型系统，它允许我们写下我们在调用 **p** /1 时所关心的事情。HM 有很多优点，允许在很多情况下进行类型推断，并且能够以一种很好的代数方式写下我们的类型。它还包括许多扩展，这对于 prolog 来说可能很方便。

那么 HM 是如何工作的呢？多亏了 Tom Schrijvers 等人的工作，我们可以用 swipl 包' [type-check](http://www.swi-prolog.org/pack/list?p=type_check) '来尝试一下。对于我们上面的 p，我们现在可以写如下:

```
:- use_module(library(type_check)).
:- type term ---> term_a ; term_b.
:- pred p(term).
p(term_a) :- write(term_a).
p(term_b) :- write(term_b). 
```

这里我们添加了一个类型“term ”,它是 term_a，或者(用“；”表示)a term_b .我们现在可以用' pred '声明来声明谓词 p 的参数。

现在，当我们加载文件时，“type_check”将尝试查找代码中存在的任何类型错误。

加载该文件的第一个结果是以下消息:

```
% TYPE WARNING: call to unknown predicate `write(term_a)'
% Possible Fixes: - add type annotation `::' to call
% - replace with call to other predicate
... :-
    'HERE'(write(term_a)).
% TYPE WARNING: call to unknown predicate `write(term_b)'
% Possible Fixes: - add type annotation `::' to call
% - replace with call to other predicate
... :-
    'HERE'(write(term_b)).
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% Type checking for module `first_blog' done:
% found type errors in 0 out of 2 clauses.
% Well-typed code can't go wrong!
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%% 
```

本质上，我们的类型检查器告诉我们它不知道任何关于 **write** /1 的事情。不幸的是，大多数标准 ISO prolog 不被类型检查所知。为了真正让它在构建大型软件时正常工作，我们需要向我们的库中添加类型注释。但是,“类型检查”提供了三种不同的解决方法。

我将依次演示前两个解决方法。

```
:- pred p(term).
p(term_a) :- write(term_a) :: write(any).
p(term_b) :- write(term_b) :< write(any). 
```

这里，我们将类型*注释*添加到代码中，通知类型检查器它可以假定 **write** /1 的参数类型为‘any’类型。这两种注释似乎达到了相同的结果，但是前者为调用添加了一个额外的包装器，确保参数实际上是适当类型的*。第二个只是对类型检查器的“信任我”风格的提示。*

 *类似地，我们可以使用下面的语法建立一个信任我风格提示的数据库:

```
:- trust_pred write(any). 
```

我们可以想象这是如何扩展到标准库的，例如:

```
:- trust_pred append(list(T),list(T),list(T)). 
```

这就确定了任何带有 append 的调用都被视为接受某个未知类型的列表、同一未知类型的另一个列表，并返回该类型的输出列表。

假设我们已经使用了这些方法中的一种来正确地对我们的谓词 p 进行类型化，我们可以看看当我们错误地使用 p 时 type_check 报告了什么。

```
:- use_module(library(type_check)).
:- type term ---> term_a ; term_b.
:- pred p(term).
p(term_a) :- write(term_a) :: write(any).
p(term_b) :- write(term_b) :: write(any).
:- pred q.
q :- p(term_c). 
```

谓词 **q** /0 是我们插入错误调用的地方。事实上，键入检查报告:

```
TYPE ERROR: expected type `unknown_type' for term `term_c'
            inferred type `term'
 in goal:
   p(term_c)
 in clause:
q :-
    'HERE'(p(term_c)). 
```

嗯，type_check 已经指出了我们的错误，现在我们可以在编译时纠正我们的错误，而不必等到生产中看到错误。

## 现实世界中的打字

这真的很棒，但是有一些警告。type_check 库确实需要解决一些问题，以便在工业实践中使用。

第一件事是，完整的标准库需要在一个库中被赋予适当的类型，这样你就不必不停地跑来跑去输入一切。为了做到这一点，type_check 需要具有模块意识，但目前还没有。模块意识也增加了很多复杂性——例如，我们如何导入类型？

第二，在边界处添加动态检查绝对是一件了不起的事情，也是让 prolog 这样的语言使用静态类型规则的正确方式。然而，我们应该什么时候应用这些类型检查呢？这就引出了模式检查的问题。我们将在下一篇关于 mavis 的文章中讨论动态类型检查和动态模式检查。*