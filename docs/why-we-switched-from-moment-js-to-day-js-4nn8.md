# 为什么我们从 Moment.js 转到 Day.js？

> 原文：<https://dev.to/ottonova/why-we-switched-from-moment-js-to-day-js-4nn8>

在执行复杂的日期时间操作时，Moment.js 是一个非常棒的库。它提供了一个丰富而干净的 API，涵盖了许多用例。除此之外，Moment.js 不应该总是成为日期时间问题的首选库。也应该考虑替代方案。

## 有哪些替代品？

事实上，有很多选择:

*   [Day.js](https://github.com/iamkun/dayjs)
*   [豪华](https://moment.github.io/luxon/)
*   [Date-Fns](https://date-fns.org/)
*   [JS-Joda](https://js-joda.github.io/js-joda/)
*   [出生日期](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)

## 为什么我们挑了 Day.js？

这一决定基于两个原因:

1.  *轻量级* -仅 **2.6KB** gzip
2.  与 Moment.js 类似的 API 这意味着更容易移植

## 迁移的怎么样了？

我们应用中使用的所有日期时间功能都位于一个名为`date.service.ts`的服务中。因此，这项服务的迁移使我们的转变成为可能。
一般来说，将日期时间操作集中在一个地方是一个很好的做法。此外，无需太多努力就能实现这样的变化。

### 迁移过程

1.  确保服务被单元测试 100%覆盖
2.  检查 Day.js 中是否有所有 Moment.js API 用法
3.  将`package.json`中的 Moment.js 替换为 Day.js
4.  调整服务以使用 Day.js

[第一步。](#steps)很简单。我们只是为我们的服务编写了缺失的单元测试。
一般来说，效用函数的测试覆盖率应该总是很高。

在[步骤 2 中。](#steps)我们发现以下变化是必要的:

*   对象实例化

```
// Moment.js
const now = moment();
const day = moment('2019-07-12'); 
```

Enter fullscreen mode Exit fullscreen mode

```
// Day.js
const now = dayjs();
const day = dayjs('2019-07-12'); 
```

Enter fullscreen mode Exit fullscreen mode

*   `diff`中的第二个参数是 Moment.js 中的*复数*，Day.js 中的*单数*

```
// Moment.js
var date1 = moment('2019-07-11');
var date2 = moment('2019-07-10');
date1.diff(date2, 'years'); // 0
date1.diff(date2, 'days'); // 1 
```

Enter fullscreen mode Exit fullscreen mode

```
// Day.js
const date1 = dayjs('2019-07-11');
const date2 = dayjs('2019-07-10');
date1.diff(date2, 'year'); // 0
date1.diff(date2, 'day'); // 1 
```

Enter fullscreen mode Exit fullscreen mode

*   UTC 支持不是随 Day.js 一起提供的
    *   应安装 dayjs-plugin-utc

```
// Moment.js
moment('2019-07-12T15:37:01+02:00').utc().format(); //2019-07-12T13:37:01Z 
```

Enter fullscreen mode Exit fullscreen mode

```
// Day.js
import dayjsPluginUTC from 'dayjs-plugin-utc';

dayjs.extend(dayjsPluginUTC);
dayjs('2019-07-12T15:37:01+02:00').utc().format(); //2019-07-12T13:37:01Z 
```

Enter fullscreen mode Exit fullscreen mode

*   `months()`不存在于 Day.js 中

```
// Moment.js
moment.months(); // ['January', 'February', ... , 'December' ] 
```

Enter fullscreen mode Exit fullscreen mode

```
// Day.js
dayjs.months(); // dayjs.months is not a function 
```

Enter fullscreen mode Exit fullscreen mode

这些 API 大多是兼容的。找到这些库之间的关键差异有助于我们解决第 3 步中的所有问题。和[第四步。](#steps)

所有其他变化都与我们的业务逻辑密切相关。

## 我们的捆绑怎么变了？

迁移证实了我们的意图。我们的捆绑包轻了 **60KB** (~10%)。
gzip moment . js 的大小是 72.47KB，现在的 Day.js 是 3.14KB(包括区域设置和 UTC 插件)

## TL；速度三角形定位法(dead reckoning)

到目前为止，切换到 Day.js 似乎是一个伟大的决定。自从一个月前迁移以来，我们没有遇到任何问题。

这篇博文的目的不是让你相信 Day.js 很棒，Moment.js 很糟糕，而 T2 很糟糕。但是要提醒你，选择日期时间库并不是一件容易的事情。
有许多选项可供选择，所以不要着急，找出最适合你的应用和需求的选项。