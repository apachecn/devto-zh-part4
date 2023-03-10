# 1 分钟内 JavaScript 中的空与未定义

> 原文：<https://dev.to/bgopikrishna/null-vs-undefined-in-javascript-in-1-min-38fo>

注意:这是我的第一篇博客，欢迎建设性的批评🙂

我总是对 JavaScript 中的`null`和`undefined`有点困惑。

现在，让我们进入主题。

当我们声明一个变量而没有给它赋值时，默认情况下它的值是`undefined`。

```
let color;
console.log(color); //undefined 
```

Enter fullscreen mode Exit fullscreen mode

但是当我们把`null`赋给一个变量时，我们是在明确地给它赋一个“空”值。

例如，我们有一个存储用户详细信息的`userDetails`变量。
起初，它没有任何数据，所以我们将`null`赋给它。

```
let userDetails = null; 
```

Enter fullscreen mode Exit fullscreen mode

稍后，我们用函数`getUserDetails`的响应填充`userDetails`变量。该功能可以是调用 API 或访问`localStorage`获取详细信息等。这里它只是一个简单的函数，返回一个对象。

```
function getUserDetails() {
  return {
    userName: 'gk',
    id: '1',
  };
}

userDetails = getUserDetails();
console.log(userDetails); // {userName:"gk", id:"1"} 
```

Enter fullscreen mode Exit fullscreen mode

如果变量定义时值是未知的，最好使用`null`。

本文最初发表在我的[博客](https://gopikrishna.dev/blog/null-vs-undefined)上。

谢谢你。

### 引用

[未定义](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/undefined)-MDN
T3】null-MDN
T6】Javascript 语法