# WebStorage APIs 及其在日常编码中的应用

> 原文：<https://dev.to/gvinod1991/webstorage-apis-and-it-s-uses-in-day-to-day-coding-3icc>

Web 存储 API 用于在浏览器中存储数据。使用 web 存储 API，我们可以在桌面浏览器中存储至少 5MB 的数据。在现代 web 开发世界中有两种最常用的机制:本地存储和 T2 会话存储。

为什么我们应该使用网络存储？

*   将用户的特定数据存储在他们自己的浏览器中，如导航历史记录、个人资料详情和其他与存储在服务器数据库中无关的数据。

*   通过 API 调用从服务器加载某些用户的特定数据在时间上可能非常昂贵。

让我们深入细节

## 本地存储

在这个 web 存储中，我们可以用键值对格式存储数据。
储存在网络浏览器中的数据，即使浏览器关闭，数据仍会存在。除非我们强制清除数据，否则数据将保持原样。根据浏览器和平台，我们可以存储至少 5 MB 或更高的容量。

### 方法

本地存储有各种方法来访问、存储、更新和删除数据。

为了保存数据

```
window.localStorage.setItem("name","John Doe") 
```

Enter fullscreen mode Exit fullscreen mode

访问数据

```
window.localStorage.getItem("name") //Output John Doe 
```

Enter fullscreen mode Exit fullscreen mode

从本地存储中删除数据

```
window.localStorage.removeItem("name") //Removes the data 
```

Enter fullscreen mode Exit fullscreen mode

更新数据

```
window.localStorage("name","Nicolas") 
```

Enter fullscreen mode Exit fullscreen mode

为了在本地存储中存储 JSON 数据，我们必须首先将 JSON 数据转换成一个字符串，然后存储它，因为 local storage 只接受字符串数据。

```
let userData=JSON.stringify({"name":"Nicolas","email_id":"nicolas@abc.com"});
window.localStorage.setItem("userData",userData); 
```

Enter fullscreen mode Exit fullscreen mode

为了访问数据，我们必须再次将字符串转换成 JSON 数据

```
let userData=window.localStorage.getItem("userData");
console.log(JSON.parse(userData)); 
```

Enter fullscreen mode Exit fullscreen mode

## [会话存储](#sessionstorage)

sessionStorage 与 localStorage 相同，只是在关闭浏览器选项卡之前，数据是可用的。数据将在特定会话中可用。它还可以存储最少 5 MB 的数据。

### 方法

为了保存数据

```
window.sessionStorage.setItem("name","John Doe") 
```

Enter fullscreen mode Exit fullscreen mode

访问数据

```
window.sessionStorage.getItem("name") //Output John Doe 
```

Enter fullscreen mode Exit fullscreen mode

从本地存储中删除数据

```
window.sessionStorage.removeItem("name") //Removes the data 
```

Enter fullscreen mode Exit fullscreen mode

这个 web 存储 API 也只接受字符串数据。