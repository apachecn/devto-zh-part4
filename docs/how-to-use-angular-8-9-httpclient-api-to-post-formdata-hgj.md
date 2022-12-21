# 如何使用 Angular 8|9 HttpClient API 发布 FormData？

> 原文：<https://dev.to/singhdigamber/how-to-use-angular-8-9-httpclient-api-to-post-formdata-hgj>

在本 **Angular FormData 教程**中，我们将了解如何使用 Angular 8 HttpClient API 将 FormData 发布到 web 服务器。表单是任何 web 或移动应用程序的重要组成部分，表单允许我们从用户那里收集数据，并将数据发送到 web 服务器。

我们将学习 FormData 对象以及如何在 Angular 应用程序中使用它。它是一个 JavaScript 对象，允许我们开发一组键/值数据结构来表示表单字段和它们各自的值。

在这篇循序渐进的文章中，我们将借助 Angular 8 HttpClient API，使用 Http POST 方法将 FormData 发送到 web 服务器。

#### 目录

1.  表单数据方法
2.  带示例的 Angular 8 表单数据教程
3.  设置角度应用程序
4.  用反应式表单和引导程序构建表单 4
5.  用 Angular HttpClient API 发布表单数据
6.  结论

## FormData 方法

您可以通过从下面给出的`new FormData`接口创建一个实例来初始化 FormData 对象。

```
const formData = new FormData() 
```

### FormData 方法

一旦创建了 **FormData** 实例，就可以通过 FormData 使用各种方法来管理数据。

| 方法 | 描述 |
| --- | --- |
| **FormData.append()** | 它包括 FormData 对象中预先存在的键的新值。如果该键已经存在，那么该值将被添加到该键中，而不会删除第一个键。 |
| **表单数据. delete()** | 它从 FormData 对象中删除一个键和值对。 |
| **FormData.entries()** | FormData 返回一个迭代器，该迭代器允许遍历对象中的一组键值对。 |
| **表单数据. get()** | 它从 FormData 对象返回与给定的一组键相关联的值。但是，如果追加了更多的值，那么它将返回第一个值。 |
| **表单数据. getAll()** | 从 FormData 对象中返回与键相关联的所有值。 |
| **FormData.has()** | 如果 FormData 对象中存在该键，则返回 true。 |
| **FormData.keys()** | 返回一个迭代器，该迭代器允许循环遍历该对象中键值数据结构的所有键。 |
| **表单数据. set()** | 它用于将值设置到具有特定键的 FormData 对象中。如果 FormData 对象中已经存在键，则替换值。 |
| **FormData.values()** | 返回一个迭代器，该迭代器允许循环遍历该对象中存在的所有值 |

[点击此处阅读更多信息](https://www.positronx.io/how-to-use-angular-8-httpclient-to-post-formdata/)