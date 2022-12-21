# 如何在 Vue 中格式化日期

> 原文：<https://dev.to/firstclown/how-to-format-dates-in-vue-42em>

如果你使用 Vue 已经有一段时间了，你很快就会发现它没有很多其他框架现成的格式选项。我感觉 Vue 非常关注极简主义，他们不想给框架添加非核心功能。不过，其中一个功能是格式化日期。

您是否曾经遇到过来自数据源的格式类似于`"2019-07-16T20:32:21"`的难看的日期，并且想“有什么最直接的方法可以使它不可读？”

我的第一个想法是使用一个计算属性，但是我很快发现这不是很好。如果我有一个嵌套在对象数组中的日期，我真的不能使用计算属性来格式化它。

我的下一个想法是在组件中使用一个方法。但是日期格式是一个非常常见的问题。我不想在一个组件中解决，我想在我所有的组件中解决。

我可以遍历数据并在对象上创建一个新的属性来保存`formattedCreateDate`和`formattedUpdateDate`，但是每次我得到一组新的数据时，我都必须重新计算所有的属性。我听着像是在哭。为什么我保存新数据是为了改变该数据的*视图*？不，那违背了我学过的所有计算机科学的东西。

Arg。

所以我四处看了看。“正确”的答案是什么？简单明了，更重要的是，可以在一个地方重用和维护。

## 视图过滤器

这是我发现 [Vue 有滤镜](https://vuejs.org/v2/guide/filters.html)的时候。过滤器是小的 UI 助手方法，允许您在视图中快速格式化数据。过滤器看起来像这样:

```
{{ name | capitalize }} 
```

Enter fullscreen mode Exit fullscreen mode

其中`name`是变量名，大写是一个过滤器，它获取 name 中的值并对其进行格式化。然后小胡子表达式将显示大写过滤器的结果。

我不知道 Vue 甚至支持这些。我用过的所有其他视图框架都有这个特性，所以我对此并不感到惊讶，但是 Vue 并没有大肆吹捧它们。它们隐藏在 Vue 文档中，但受到支持。

这就是我想要的约会方式。我有数据，只是想为*这个*视图设置不同的格式。

## 一个用于 Vue 的简单日期过滤器

所以简单的答案是，使用处理日期的过滤器。这里有一个:[https://github.com/eduardnikolenko/vue-filter-date-format](https://github.com/eduardnikolenko/vue-filter-date-format)

这个过滤器将获取你的日期，并以指定的格式显示出来。首先，您需要一个日期对象。如果您没有将日期作为日期对象存储在数据属性中，您可以使用[https://github.com/eduardnikolenko/vue-filter-date-parse](https://github.com/eduardnikolenko/vue-filter-date-parse)来转换对象中的日期字符串。

```
{{ '2019-07-16' | dateParse('YYYY-MM-DD') }} 
```

Enter fullscreen mode Exit fullscreen mode

然后使用`dateFormat`过滤器以更加用户友好的方式显示日期:

```
{{ '2019-07-16' | dateParse('YYYY-MM-DD') | dateFormat('MMMM D, YYYY') }} 
```

Enter fullscreen mode Exit fullscreen mode

这些过滤器的好处是它们不会引入任何额外的库。它们的重量非常轻，只包含使用标准 JavaScript 日期对象函数的过滤函数。 <sup id="fnref1">[1](#fn1)</sup>

这涵盖了我 90%的约会需求，并且不会增加我的代码。但是如果你需要更强壮一点的东西呢？

## Vue 中更复杂的日期过滤器

JavaScript 中最大的日期库是 [Moment.js](https://momentjs.com/) 。Moment.js 可以做*任何事情*。如果你正在寻找像`2 hours ago`或`Last Saturday at 9:00 PM`这样的日期格式，那么你将需要像 Moment.js 这样的东西。幸运的是，有一个[方便的过滤器也使用 Moment.js](https://github.com/brockpetrie/vue-moment) ，并将为你提供所有这些惊人的功能:

```
{{ '2019-07-16' | moment('from', 'now') }} 
```

Enter fullscreen mode Exit fullscreen mode

除非你绝对需要这种高级功能，否则我不会使用这个过滤器。Moment.js 不是一个小的库，仅仅为了进行简单的日期操作而将它加载到 web 页面中是不值得的。

所有这些都是从 NPM 安装的，并直接导入到您的`main.js`文件中。他们也有很好的文档，所以检查一下，永远解决你所有的 Vue 日期问题。

* * *

1.  可以在这里看一下[滤镜功能。](https://github.com/eduardnikolenko/vue-filter-date-format/blob/master/src/filters/dateFormat.ts) [↩](#fnref1)