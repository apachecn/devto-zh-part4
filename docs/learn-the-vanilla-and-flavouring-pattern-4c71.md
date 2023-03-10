# 学习香草和调味模式

> 原文：<https://dev.to/giteden/learn-the-vanilla-and-flavouring-pattern-4c71>

*原文由乌·考特纳写于*

### 你能为一个漂亮的设计写出既可重用又足够具体的代码吗？

这种模式试图解决编码中的一个难题:如何使代码既通用又稳定，同时又具有可替换性和特殊性？

## 可重复使用的特定代码

在自顶向下的方法中，人们从头脑中的结果开始，并快速迭代代码，直到产生期望的结果。你可能知道这叫做[测试驱动开发](https://blog.bitsrc.io/a-guide-to-test-driven-development-tdd-shorter-feedback-loop-faster-workflow-ce5bd6b247c4)，但是对于你这个测试人员来说是一样的。

[![A button from [Bit’s UI](https://bit.dev/)](img/9dcfff500572c9b6a6cdbce9fc56f98f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q5sJarcI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AYMAkT-Z2YrB1fPqDStpOtQ.png) 
一个按钮来自 [Bit 的可复用组件平台](https://bit.dev/)

很简单。按钮是一个简单的组件，具有边框、边框半径和颜色。你甚至可以给它一个小的变形，这样当你点击它的时候它就会做出反应。到目前为止还不错。