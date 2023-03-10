# 你应该写测试的 3 个理由

> 原文：<https://dev.to/georgehanson/3-reasons-why-you-should-write-tests-c7e>

我认为编写测试是很多开发人员后来都会做的事情。当开发人员学习编码时，不管是通过训练营还是在大学自学，这是很常见的；测试经常被忽视。

我就不明白了，为什么测试不够专注？当您开始在生产项目和团队中工作时，您经常会发现测试是您必须要做的事情。

我认为部分原因在于编写的测试不够清晰。这就像开发人员版的办公室文书工作——似乎没有人想做。让我解释一下为什么你**应该**编写自动化测试。

## 典型场景

在大多数工作场所，典型的场景是这样的。您正在处理一个 API 的 REST 端点。开始时相当简单，但随着时间的推移，它变得越来越复杂。不久之后，你就要使用存储库、缓存，这一切都变得令人望而生畏。因为你担心会破坏代码，所以你不会重构代码，随着时间的推移，它变成了人们不想使用的意大利面条式代码。

测试使这变得更好。

## 什么是自动化测试？

简而言之，自动化测试就是确保你的代码做它应该做的事情。您与自己创建的 API 进行交互，并断言正确的操作已经发生。无论这是一个 HTTP 响应代码，检查记录是否存在于数据库中，或者只是别的什么。

同样值得一提的是，测试不是灵丹妙药。它遵循和大多数 GiGo 一样的原则(垃圾入，垃圾出)。你必须确保你写的测试是有意义的和有用的，否则它们没有任何意义。

## 常用测试框架

对于测试来说，最简单的开始方式是使用许多适合您选择的语言的框架中的一个。下面是一些 PHP 和 JavaScript 常用的测试框架。

### PHP

*   PHPUnit
*   共同认知
*   贝哈特

### JavaScript

*   玩笑
*   茉莉
*   摩卡

## 为什么你应该写自动化测试？

所以我已经介绍了自动化测试有用的几个原因。但是让我更深入地解释一下为什么您应该编写自动化测试。

### 1。开发者信心

正如我上面提到的，创建这些笨重的大类并不需要很长时间。测试给你的最好的好处之一是作为一个开发者的自信。你可以做任何你想做的改变，只需运行测试套件，就能立刻知道你是否破坏了任何东西。

这给了你很大的灵活性。您不仅可以无忧无虑地重构代码，还可以尝试解决方案，而不必担心破坏代码。

### 2。作为消费者思考

测试通常需要你从不同的角度思考。你必须摘下开发者的帽子，戴上消费者的帽子。无论这是一个人消费 HTTP API 还是一个包的方式，你都可以用最适合消费者的方式来设计你的系统。

因此，它允许你很容易地想到用户可能选择如何与你的应用程序交互的边缘情况。有多少次用户以你意想不到的方式使用应用程序？

### 3。节约时间

与很多人想的相反，测试**确实能节省你的时间。当你在编写测试和你所要求的实现时，这怎么可能呢？因为它让你更容易发现错误，所以你不太可能一开始就花时间去调试。如果您的系统中报告了一个 bug，编写一个测试来重现这个 bug，然后您就可以单独与应用程序进行交互来找到根本原因。**

测试是你花时间写的东西，是对未来的投资。

## 有没有什么时候你不应该写自动化测试？

一般来说没有。但是记住这不是一条法律，用你最好的判断。如果你正在编写一个小的应用程序，只是为了玩一个想法，并且你不打算把它发布给外界，那么不写任何测试也没有坏处。也就是说，养成这种习惯真的很好，你做得越多，它就会变得越容易。