# Web 应用程序中的脱机数据入门。2

> 原文：<https://dev.to/remotesynth/getting-started-with-offline-data-in-web-apps-pt-2-39o2>

在这一系列的文章中，我正在寻找一些离线处理数据的方法。[第一部分](https://dev.to/remotesynth/getting-started-with-offline-data-in-web-apps-pt-1-136a)探索了用于确定用户是否离线或连接速度慢/差的选项。在这一部分中，我们将研究一些存储数据的选项，当用户离线时我们可以访问这些数据，或者甚至为那些连接不良的用户缓存这些数据。让我们从存储少量相对简单的数据开始，并探索使用 [localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) 来存储这些数据。

## 什么是 localStorage？

localStorage 最好的地方在于它既容易理解又容易使用。基本上，localStorage 是一个脱机键/值存储。与拥有相同 API 的`sessionStorage`不同，这些数据将跨浏览器会话保存。因此，当用户离线时，它对访问数据很有用。

然而，它有一些重要的限制。例如，它只能保存字符串值，但这允许您存储序列化数据。也是[同步](https://www.quora.com/Is-localStorage-in-HTML5-always-synchronous)。它只允许每个域大约 5MB 的存储配额(具体数量可能因浏览器而略有不同)。

> 如果超过这个限额会怎么样？我找不到任何关于这个话题的文章，但是 Raymond Camden 的这篇文章探索了每种浏览器的行为有所不同。

没有内置的数据保护——域中的任何 JavaScript 代码都可以访问 localStorage。事实上，您可以简单地打开浏览器工具，以纯文本格式查看所有 localStorage 值。您甚至可以通过控制台编辑任何值。这种不安全性导致一些人建议根本不要使用本地存储。

尽管有局限性，localStorage 仍然可以用于存储简单的值，这些值在应用程序脱机时维护应用程序的状态。

## 使用本地存储

localStorage 的 API 非常简单。你用`setItem()`设置一个项目，用`getItem()`得到一个项目。

```
localStorage.setItem('keyName', value);

let myData = localStorage.getItem('keyName'); 
```

Enter fullscreen mode Exit fullscreen mode

您还可以使用`localStorage.removeItem('keyName')`删除单个项目，或者使用`localStorage.clear()`清除您的域的所有本地存储。

如果您想使用 localStorage 存储比简单字符串更复杂的内容，您需要序列化和反序列化数据。

```
localStorage.setItem('complexData', JSON.stringify(data));
let complexData  = JSON.parse(localStorage.getItem('complexData')); 
```

Enter fullscreen mode Exit fullscreen mode

另一件要提到的事情是，您可以监听 localStorage 上的事件。这将返回一个`StorageEvent`,为您提供关于被修改的键以及旧值和新值的详细信息。这在进行更改的同一个页面上不起作用—这实际上是域中使用存储的其他页面与所做的任何更改同步的一种方式。

## 举例

为了给出所有这些概念的一个简单例子，我创建了一个 CodePen，它调用一个远程 API 来用饮料类型填充一个`<select>`列表。因为这个数据相对较小，所以我将它缓存在 localStorage 中，这样即使用户离线，列表也会被填充。如果您从列表中选择一个项目，它也将存储并保持该偏好。

[https://codepen.io/remotesynth-the-bold/embed/PrLKPB?height=600&default-tab=js,result&embed-version=2](https://codepen.io/remotesynth-the-bold/embed/PrLKPB?height=600&default-tab=js,result&embed-version=2)

这个演示目前非常简单，但是在未来的迭代中，我将使用这个选项从 API 中提取更复杂的数据，并使用 IndexedDb 离线存储和检索这些数据。顺便说一句，在 CodePen 上发布这么难看的演示，看到人们在那里创造的令人惊叹的东西，我几乎感到内疚。说设计不是我的强项是一种严重的保守说法。

## 下一步

我们已经看到 localStorage 有一个简单的 API，可以很容易地用来离线存储某些类型的数据。如上所述，从功能和安全性的角度来看，它都有一些限制，您需要了解这些限制。但是，如果您需要离线存储大量复杂数据，该怎么办呢？这就是我们希望在本系列的下一部分开始研究 IndexedDb 的地方。