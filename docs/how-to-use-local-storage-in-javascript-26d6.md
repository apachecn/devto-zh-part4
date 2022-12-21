# JavaScript 中如何使用本地存储？

> 原文：<https://dev.to/bgopikrishna/how-to-use-local-storage-in-javascript-26d6>

*这篇文章最初发表在我的[博客](https://bgopikrishna.github.io/blog/localstorage-in-javascript)上。请随意查看*

本地存储允许我们将数据存储在用户的浏览器中。即使关闭浏览器，存储的数据也不会被删除。

数据将以键/值对的形式存储。键和值总是字符串。

### 存储数据

例如，我们有一个用户，他的偏好/设置需要存储在`localStorage`中。让我们用用户设置创建一个`userPreferences`对象。

```
const userPreferences = {
    darkMode: true,
    accentColor: 'red',
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们将使用`localStorage.setItem`方法
将其存储在浏览器中

```
localStorage.setItem("userData", JSON.stringfy(userPreferences)) 
```

Enter fullscreen mode Exit fullscreen mode

这里`userData`是键，`userPreferences`是值。我们使用`JSON.stringfy`方法的原因是将`userPreferences`对象转换成一个字符串。

### 检索数据

当我们需要使用数据时，我们可以通过将键作为参数传递来使用`localStorage.getItem`方法检索它。返回的数据将是字符串的形式，所以我们需要使用`JSON.parse`方法将其转换成对象。

```
let userData = localStorage.getItem("userData") //"{ darkMode: true, accentColor:'red'}"

//convert the string to an object
userData = JSON.parse(userData)

console.log(userData) // { darkMode: true, accentColor: 'red'} 
```

Enter fullscreen mode Exit fullscreen mode

### 清除数据

如果要清除存储在本地存储器中的所有数据，我们需要使用`localStorage.clear`方法。

```
localStorage.clear() 
```

Enter fullscreen mode Exit fullscreen mode

### 删除特定数据

如果你想删除特定键的数据，我们需要使用`localStorage.removeItem`方法，把 key 作为参数传递。

```
localStorage.removeItem("userData"). 
```

Enter fullscreen mode Exit fullscreen mode

### 会话存储

`sessionStorage`与`localStorage`相同。但主要区别是，当用户关闭网页时，存储的数据会自动清除。所有的方法也是一样的。

### 局限性

不要在本地存储中存储任何敏感数据，因为任何浏览页面的人都可以访问这些数据。并且没有任何形式的数据保护。

### 举例

如果你正在 *[开发者到](https://dev.to)* 上阅读这篇文章，按`ctrl(cmd) + shift + I`打开开发者工具，进入**应用→本地存储**。在这里，您将看到 *[如何开发到](https://dev.to)* 使用本地存储来存储带有键`current_user`的用户详细信息。

如果你正在我的博客上阅读这篇文章，重复上面的步骤，你将会看到我是如何使用本地存储通过按键`darkMode`来保持黑暗模式的。

谢谢你。

### 引用

[LocalStorage - MDN](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)

[会话存储- MDN](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage)