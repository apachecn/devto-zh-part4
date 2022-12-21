# JavaScript URL 对象简介

> 原文：<https://dev.to/attacomsian/introduction-to-javascript-url-object-27hn>

*本文最初发表于[attacomsian.com/blog](https://attacomsian.com/blog/javascript-url-object)。*

* * *

普通 JavaScript `URL`对象用于解析、构造、规范化和编码 URL。它提供了静态方法和属性来轻松地读取和修改 URL 的不同组成部分。

## 创建 URL

您可以创建一个新的`URL`对象，要么传递字符串 URL，要么提供一个相对路径和一个基本字符串 URL 给它的构造函数:

```
// url object with absolute path
const url1 = new URL('https://attacomsian.com/blog/javascript-url-object');

// url object with relative path and base url
const url2 = new URL('/blog/javascript-url-object', 'https://attacomsian.com'); 
```

在上面的例子中，两个 URL 是相同的。我们甚至可以基于相对于现有 URL 的路径创建一个新的 URL:

```
// create new url based on existing url
const url3 = new URL('/blog', url1);

console.log(url3.toString()); // https://atttacomsian.com/blog 
```

## URL 属性

如果您想从一个字符串 URL 中提取不同的部分，比如主机名、端口、相对路径和参数值，那么`URL`对象非常有用。在创建了`URL`对象:
之后，您可以立即访问这些[属性](https://developer.mozilla.org/en-US/docs/Web/API/URL#Properties)

```
const url = new URL('https://attacomsian.com/blog/javascript-url-object#url-properties');

console.log(url.protocol); // https:
console.log(url.host);     // attacomsian.com
console.log(url.pathname); // /blog/javascript-url-object
console.log(url.hash);     // #url-properties
console.log(url.origin);   // https://attacomsian.com 
```

除了上述属性，`URL`对象还具有:

*   `search` —包含前导`?`字符的查询参数字符串。
*   `href` —完整的 URL，与`url.toString()`方法相同。
*   `port` —返回 URL 的端口。
*   `searchParams`—`URLSearchParams`对象，可用于访问在`search`中找到的单个查询参数。
*   `username` & `password` —仅在使用 HTTP 认证时可用。

除了上述属性，`URL`对象还提供了两种方法:

*   `toString()` —类似于`url.href`，但不能用来修改数值。
*   `toJSON()` —返回与`href`属性相同的字符串。

## 更新网址

`URL`对象属性(除了`origin`和`searchParams`之外)可以用来构造一个新的 URL 或者更新现有 URL 的一部分:

```
// construct a url
const url = new URL('http://attacomsian.com');
url.pathname = '/blog/javascript-url-object';
url.hash = '#url-properties';

// update `protocol` property
url.protocol = 'https:';

console.log(url.toString()); // https://attacomsian.com/blog/javascript-url-object#url-properties 
```

## 静态方法

`URL`接口提供了`createObjectURL()`静态方法来生成一个 blob URL(以`blob:`作为其模式开始),该 URL 唯一地标识了浏览器中的对象:

```
const blobUrl = URL.createObjectURL(blob); 
```

一旦有了 blob URL，就把它传递给`revokeObjectURL()`静态方法，从内存中删除它:

```
URL.revokeObjectURL(blobUrl); 
```

## URL 对象用法

目前，`URL`对象的使用是有限的。简单的字符串足以满足网络请求。但是，您可以在现代 JavaScript APIs 中使用`URL`对象，尤其是在 [Fetch API](https://attacomsian.com/blog/javascript-fetch-api) 中，甚至在 [XMLHttpRequest (XHR)](https://attacomsian.com/blog/http-requests-xhr) 中与服务器通信。

下面是一个 Fetch API 的例子，它使用一个`URL`对象来获取一个 JSON 对象:

```
const url = new URL('https://reqres.in/api/users');

fetch(url)
    .then(res => res.json())
    .then(res => {
        res.data.map(user => {
            console.log(`${user.id}: ${user.first_name}  ${user.last_name}`);
        });
    }); 
```

* * *

✌️:我写了关于现代 JavaScript、Node.js、Spring Boot 和所有 web 开发的东西。 [**订阅我的简讯**](https://attacomsian.com/newsletter) 每周获取 web 开发教程& protips。

* * *

**喜欢这篇文章？** [在推特上关注@ attacomsian](https://twitter.com/attacomsian)。你也可以在 [LinkedIn](https://linkedin.com/in/attacomsian) 和 [DEV](https://dev.to/attacomsian) 上关注我。