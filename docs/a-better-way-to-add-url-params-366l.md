# 添加 URL 参数的更好方法

> 原文：<https://dev.to/rrjoson/a-better-way-to-add-url-params-366l>

假设您正在为一个搜索页面构建过滤器组件。为了实现这一点，您可以使用模板字符串来构建您的 url 参数。

```
const urlParams = `?checkin=${filter.values.checkin}&checkout=${filter.values.checkout}`; 
```

这可能看起来不错，但当它获得更多的值时，可能会变得有点难以阅读。

另一种方法是使用[查询字符串](https://github.com/sindresorhus/query-string)。它提供了一个 stringify 函数，允许您传递一个对象，并为您生成 url 参数。

```
import queryString from 'query-string';

const urlParams = queryString.stringify({ 
   checkin: filter.value.checkin,
   checkout: filter.value.checkout
}) 
```

结果是一种无需手动添加&就能生成 URL 参数的方法。当您必须传递多个值时，阅读起来也更容易。