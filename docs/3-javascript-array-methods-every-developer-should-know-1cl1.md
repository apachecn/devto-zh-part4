# 每个开发人员都应该知道的 3 个 JavaScript 数组方法

> 原文：<https://dev.to/lelouchb/3-javascript-array-methods-every-developer-should-know-1cl1>

<figure>[![](img/f4d1aa67d91923e5ce14a2b469516c85.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D34WJ-_V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AckWxE4b3zxi1bgZoJFFxOw.jpeg) 

<figcaption>照片由[卡斯帕·卡米尔·鲁宾](https://unsplash.com/@casparrubin?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上传 [Unsplash](https://unsplash.com/search/photos/javascript?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

在本文中，我们将讨论:

1.  加入()
2.  拆分()
3.  排序()

为什么每个 JavaScript 开发人员都应该知道这些方法？数组是代码中需要的重要元素。这些方法有助于使您的代码更优雅、更美观。

您可以让您的项目在没有这些方法的情况下运行，但是为此，您将不得不编写不必要的代码行，这些代码行本来就没有用。

让我们开始吧。我们将首先看到一些基本的方法，如 join()和 split()，然后我们将转到 sort()。

### **1。Join()**

设想这样一个场景，用户在数组中输入一些值，然后希望将其作为一条消息(字符串)来查看。

这就是 join()方法的用武之地，它将数组元素转换成字符串。

toString()也用于将字符串转换为数组，但是使用 join()时，我们可以使用分隔符参数，所以最好使用 join()。

语法非常简单，您只需使用:

array.join(分隔符)

这里，分隔符对于传递的参数是可选的，用来定义希望数组中的元素如何分隔。它可以是空格、点、逗号、单词等。

如果没有传递参数，其元素用逗号分隔。

让我们看看它的实际效果