# 改进我的前端诊断模式

> 原文：<https://dev.to/rfornal/improving-my-front-end-diagnostic-pattern-2aan>

## 我在哪里...

最近在查看一些客户端代码时，我遇到了一个与我以前使用过的模式相似的模式。虽然我可以看到相似之处，但模式的切换是完全不同的。

在过去创建脚本引擎和解析器的过程中，我使用控制台日志来显示简单的诊断信息。我不想让日志记录出现在产品中，同时也不想删除我创建的日志记录器和测试信息，所以我决定创建一个简单的根级布尔值，日志记录器将监视它。

```
var forceTesting = false; 
```

有了这些代码，我就可以在控制台中简单地运行以下代码，然后在观察输出的同时使用该站点:

**> forceTesting = true**

## 有些事我做对了...

我尽量高效地使用控制台 API 如果您还没有研究过控制台 API，请花些时间去研究一下，因为有很多可用的功能。以下是我使用的控制台 API 的一些部分...经常有开发者说，“我能做到吗？”

[文档](https://developer.mozilla.org/en-US/docs/Web/API/Console)

### 控制台.计数

记录这个特定的 count 调用被调用的次数。这个函数有一个可选的参数标签。

> console.count('迭代')；
> console.countReset('迭代')；

### 控制台

在 Web 控制台日志中创建新的内联组。在调用 console.groupEnd 之前，将后续控制台消息缩进一个额外的级别。

> console . group(“action”)；
>console . group collapsed(' action ')；
>console . groupend(' action ')；

### 控制台.表

以表格形式显示表格数据。它以表格的形式记录数据。数组中的每个元素或可枚举属性(如果数据是对象)都将是表中的一行。

> console . table([' items '])；

### 控制台.时间

启动一个计时器，该计时器可以跟踪一个操作需要多长时间。每个计时器都有一个唯一的名称。当以相同的名称调用 console.timeEnd 时，浏览器将输出计时器启动后经过的时间(以毫秒为单位)。

> console . time(' label ')；
>console . timelog(' label ')；
>console . time end(' label ')；

## 我发现了什么...

现在让我们看看我遇到的客户机代码。

在客户端代码中，我发现...

```
var forceTesting = JSON.parse(localStorage.getItem('forceTesting') || false); 
```

这允许他们的开发人员执行一些熟悉的东西，他们的代码被配置，像我的代码一样，显示日志或使用不同的数据集。

**>local storage . setitem(' force testing '，true)**

我很快就明白了这段代码的真正精髓。一些简单的控制台测试证实了我的想法。

这段代码不仅开启了“开发人员模式”，还为我的环境设置了一致的模式。使用 Chrome 的“清空缓存和硬重新加载”**清除缓存并没有**移除 localStorage forceTesting 状态。我仍处于开发模式。

## 总结

我的模式受到一个简单事实的困扰，即每次重新加载代码时，我都必须更改全局变量的值。这种新模式允许在整个开发周期中维护状态！