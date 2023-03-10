# 你如何命名你的测试？

> 原文：<https://dev.to/n_develop/how-do-you-name-your-tests-108c>

您可能知道，我是单元测试和 TDD 的忠实粉丝。但是我经常在单元测试的命名上来回摇摆。单元测试有许多流行的命名方案。其中一些是:

## 【测试方法】_【测试状态】_【预期行为】

这是一个经典的命名方案。

> `ReadFrom_FileDoesNotExist_ThrowsException`

## 【测试方法】_【预期行为】_【测试状态】

这与第一个方案非常相似。个人觉得这个比第一个更喜欢一点。这本读起来更自然一点。例如:

> `ReadFromFile_ThrowsException_FileDoesNotExist`

它读起来就像“如果文件不存在，WriteToFile 抛出异常”。

## 应该 _[预期行为]_ 当 _[测试状态]

> `Should_ThrowException_When_FileDoesNotExist`

这读起来也几乎像一个“正常”的句子。

## When _[测试状态]_ Expect _[预期行为]

> `When_FileDoesNotExist_Expect_WriteToFailToFail`

## 给定 _[前提条件]_ When _[测试状态]_ Then _[预期行为]

> `Given_ReportIsWrittenToFile_When_FileDoesNotExist_Then_ExceptionIsThrown`

## 只是描述正在发生什么和应该发生什么

这是我时常回想起的事情。在这些情况下，我的测试显示如下:

> `OrderProcessorThrowsAnExceptionInCaseOfMissingCustomer`

这些只是几个例子。欢迎在评论中添加更多的命名方案。

你最喜欢的命名方案是什么？有你不喜欢的命名方案吗？