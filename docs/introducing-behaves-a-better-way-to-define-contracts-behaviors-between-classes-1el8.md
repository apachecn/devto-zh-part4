# 引入“行为”,一种定义类之间契约/行为的更好方法

> 原文：<https://dev.to/edisonywh/introducing-behaves-a-better-way-to-define-contracts-behaviors-between-classes-1el8>

嘿伙计们！

我刚刚在 RubyGems 上发表了我的第一个 gem，希望听到你的反馈！

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [ edisonywh ](https://github.com/edisonywh) / [水师](https://github.com/edisonywh/behaves)

### 定义代码之间的行为和契约。

<article class="markdown-body entry-content container-lg" itemprop="text">

[![CircleCI Badge](img/7f1b2ecf4aff1e9245ceddab64fbb9e3.png)](https://camo.githubusercontent.com/fe4b2004b0fa587c5b305baa734376d3e945fdf112930531fdb35c5f29f1bcce/68747470733a2f2f696d672e736869656c64732e696f2f636972636c6563692f6275696c642f6769746875622f656469736f6e7977682f626568617665732e737667)[![RubyGems Badge](img/87482cdad2b82322a445f60bb4ecfc1b.png)](https://camo.githubusercontent.com/0b0a4f7477541cf302f78291cd54539411ffcb262ada6dc17fabae91d5fd9f86/68747470733a2f2f696d672e736869656c64732e696f2f67656d2f762f626568617665732e737667)[![Code Climate maintainability](img/a05ab2f10c5ebbb3a497ff66d07f7d0e.png)](https://camo.githubusercontent.com/e20b8dcad18b43fa20e8380aee28949fc4f68904ad91a79ff7e09cfc81d7884d/68747470733a2f2f696d672e736869656c64732e696f2f636f6465636c696d6174652f6d61696e7461696e6162696c6974792f656469736f6e7977682f626568617665732e737667)

# 表现

behaviors 是帮助您定义类之间行为的一块宝石。定义行为时告别运行时错误。

通过确保所有适配器都定义了所需的行为，行为对于处理适配器模式特别有用。更多示例见下面的[用法。](https://github.com/edisonywh/behaves#usage)

*以下章节中的详细解释。*

## 装置

将这一行添加到应用程序的 Gemfile 中:

```
gem 'behaves'
```

Enter fullscreen mode Exit fullscreen mode

## 使用

这就是你如何用`behaves`定义行为。

首先，用`implements`方法在`Behavior Object`上定义所需的方法，这需要一个方法列表。

```
class Animal
  extend Behaves
  implements :speak, :eat
end
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以通过使用`behaves_like`方法(需要一个`Behavior Object`)将任何对象(`Behaving Object`)转变为类似于`Behavior Object`的行为。

```
class Dog
  extend Behaves
  behaves_like Animal
end
```

Enter fullscreen mode Exit fullscreen mode

瞧，这就是定义行为所需要的一切！现在如果`Dog`没有实现`speak`并且…

</article>

[View on GitHub](https://github.com/edisonywh/behaves)

## 描述

Behaves 是一块宝石，可以帮助你维护不同职业之间的契约。通过确保所有适配器都定义了所需的行为，这对于处理适配器模式特别有用。

关于`Behaves`的想法源于我对 Ruby 中的[适配器模式的研究，以及 José Valim 关于](https://www.sitepoint.com/using-and-testing-the-adapter-design-pattern/)[模仿和显式契约](http://blog.plataformatec.com.br/2015/10/mocks-and-explicit-contracts/)的文章。

我发现当前在 Ruby 中实现`behaviors`的习语是通过`Inheritence`，然后随后定义一个“required ”(我用引号将它括起来，因为在运行它之前它并不完全是`required`)方法，它除了引发一个`NotImplementedError`之外什么也不做。虽然我不一定认为*不好*，但我确实认为可能有一种替代方案，那就是**更明确**，**更少的样板**，**更清晰的祖先层级**，从而诞生了`Behaves`。

最棒的是？定义行为时不再有运行时错误！

一定要让我知道你的想法！