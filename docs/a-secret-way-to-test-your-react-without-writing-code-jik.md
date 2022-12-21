# 一个不用写代码就能测试你反应的秘密方法。

> 原文：<https://dev.to/spearmintjs/a-secret-way-to-test-your-react-without-writing-code-jik>

编写测试就像吃蔬菜:这是每个人都知道他们应该做的事情，但不是每个人都这样做。Spearmint 提供了一种简单的方法来编写测试，让您相信 React 应用程序将按照用户与它交互的方式工作。

使用 Spearmint，开发人员可以通过使用我们用户友好的 GUI 轻松地生成 React 测试。它通过使用 react-testing-library 提供的 DOM 查询选择器，动态地将用户输入转换成可执行的 Jest 测试代码。我们选择 react-testing-library 而不是 Enzyme，因为它引导开发人员将注意力集中在测试用户行为上，而不是测试实现细节。通过利用 react-testing-library，我们的应用程序为我们的用户提供了更稳定的测试，在重构时不会中断，也不会由于浅层渲染而产生误报。

在:
[www.spearmintjs.com](http://www.spearmintjs.com)
[www.github.com/spearmintjs/spearmint](http://www.github.com/spearmintjs/spearmint)拜访我们