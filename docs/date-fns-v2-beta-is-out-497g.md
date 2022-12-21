# 日期-fns v2 测试版出来了！

> 原文：<https://dev.to/kossnocorp/date-fns-v2-beta-is-out-497g>

对于那些不知道的人来说，date-fns 是一个现代的 JavaScript 日期实用程序库。它专注于构建规模和性能。它是树摇动的，所以只有用过的功能会被包含在你的构建中。最小构建大小只有 295 B，这使得它成为 JS 世界中最小的数据库！与其他日期库不同，date-fns 使用原生的`Date`对象，并采用函数式方法。

我们花了两年时间来开发 v2，在此期间，我们修改了库的每一部分。我们已经合并了来自 100 个贡献者的近 500 个拉请求！今天我邀请你试一试:

```
npm install date-fns@next --save
# or using Yarn:
yarn add date-fns@next 
```

它很稳定，我们不打算改变 API。这可能是一个最终版本，但由于它在未来几天引入了大量突破性的变化，我们将努力使升级过程尽可能顺利。同时，你可以通过测试库和分享你的反馈来帮助我们。

## 有什么新鲜事？

这里是一些最令人兴奋的特性，包括所有的变化(这是巨大的！)见变更日志:[https://date-fns.org/v2.0.0-beta.1/docs/Change-Log](https://date-fns.org/v2.0.0-beta.1/docs/Change-Log)

**EcmaScript 模块**。对于 v2，我们同时提供 ESM 和 CommonJS，因此如果您的捆绑器支持树抖动，您可以直接从包的根目录导入函数，并且仍然得到精简版本:

```
import { format, formatDistance, formatRelative, subDays } from 'date-fns'

format(new Date(), "'Today is a' iiii")
//=> "Today is a Wednesday"

formatDistance(subDays(new Date(), 3), new Date())
//=> "3 days ago"

formatRelative(subDays(new Date(), 3), new Date())
//=> "last Friday at 7:26 p.m." 
```

此外，我们采用了 camelcase 命名方案:

```
// Before v2.0.0
import addDays from 'date-fns/add_days'

// v2.0.0 onward
import addDays from 'date-fns/addDays' 
```

另一个我确信 FP 粉丝会喜欢的特性是新的 **FP 子模块**。它引入了常规函数的副本，这些函数默认接受逆序的参数。它们可以从 date-fns/fp 导入，并与常规函数一起使用。

FP 函数的主要优势是支持[函数式的函数组合](https://medium.com/making-internets/why-using-chain-is-a-mistake-9bc1f80d51ba)。

```
const { differenceInDays: regularDifferenceInDays } = require('date-fns')
const { differenceInDays: fpDifferenceInDays } = require('date-fns/fp')

regularDifferenceInDays(Date.now(), 0)
//=> 17815

fpDifferenceInDays(0, Date.now())
//=> 17815

fpDifferenceInDays(0)(Date.now())
//=> 17815

const daysSinceUnixEpoch = fpDifferenceInDays(0)
daysSinceUnixEpoch(Date.now())
//=> 17815 
```

我们增加了几十个新功能，但是有一个需要特别注意:`parse`。它允许使用任意格式解析字符串:

```
import { parse } from 'date-fns'

parse('02/11/2014', 'MM/dd/yyyy', new Date())
//=> Tue Feb 11 2014 00:00:00 
```

这可能是最受欢迎的功能，为了实现它，我们完全重写了 I18n 代码。

此外，我们仔细细化了每个函数，以使 date-fns 一致、可预测且可靠。我们让它像 ECMAScript 一样在边缘情况下工作。你可以[在一篇开发文章](https://dev.to/kossnocorp/date-fns-v2-api-design-2jig)中阅读更多关于 v2 API 设计的内容。

## 接下来是什么？

在我们发布最终版本后，我们希望关注几个计划:

*   函数的 UTC 版本。
*   持续时间支持。
*   时区(目前 TZ 功能由 [date-fns-tz](https://www.npmjs.com/package/date-fns-tz) 提供)。
*   与 Intl API 集成。

## 鸣谢

总的来说，如果没有 153 个贡献者的帮助，v2 和 date-fns 都是不可能的。我希望我能提到每一个人，但是名单太长了。但是我不能跳过我的哥哥， [Lesha Koss](https://twitter.com/leshakoss) ，他写了大部分的日期-fns 代码。你太棒了！

*感谢您的阅读！我希望你会喜欢 v2。加入 Spectrum 的 [date-fns 社区，并在 Twitter](https://spectrum.chat/date-fns) 上关注我们。*