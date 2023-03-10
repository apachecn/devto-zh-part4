# JSON 是谁？

> 原文：<https://dev.to/shimphillip/who-is-json-13h6>

JSON 是一种轻量级的基于文本的数据，代表了 **JavaScript 对象符号**。当在网络上传输数据和在本地存储中存储数据时，这种格式是一种流行的选择。您还应该注意到，它是一种独立的数据格式，也可以被其他编程语言使用。

JSON 通常由两种不同的格式表示:JSON 字符串或 JSON 对象。JSON 对象与 JavaScript 对象非常相似，只是它的**键和值需要用双引号**括起来。它们通常存储在扩展名为`.json`的文件中。

```
// phillip.json
{
  "name": "Phillip",
  "age": 29,
  "languages": ["JavaScript", "Python", "C++"],
  "appearance": {
    "hairColor": "black",
    "eyeColor": "Brown"
  }
} 
```

*JSON 对象也是有效的 JavaScript 对象！*

* * *

## 网络响应中的 JSON

当调用 API 时，通常会看到 JSON 作为响应。让我们使用 fetch 从 phillip.json 检索一些数据到同一个目录下的 JavaScript 文件中。

```
// index.js
fetch("./phillip.json").then(response => console.log(response)) // response object 
```

控制台记录收到的数据给我们一个 HTTP 响应对象，而不是我们正在寻找的实际 JSON。为了检索我们想要的东西，我们需要使用内置的。HTTP 响应对象给出的 json()方法。它现在将返回一个包含 JSON 的承诺。

```
// index.js
fetch("./phillip.json")
  .then(response => response.json())
  .then(data => console.log(data)) // phillip object! 
```

* * *

## JSON 在 localStorage 中用 stringify() & parse()

当我们希望即使在应用程序关闭后也能持久存储数据时，LocalStorage 是将数据存储为键/值对的一个方便的选项。可以把它想象成一个本地数据库。不过有一点需要注意:**对象和数组不能存储在 localStorage** 中。

```
// Set an item and immediately get it back
fetch("./phillip.json")
  .then(response => response.json())
  .then(data => {
    localStorage.setItem("profile", data)
    localStorage.getItem("profile") // [object Object] - what is this???
}); 
```

救援来了， **JSON.stringify()** 。这个方法将 JSON 对象转换成一个字符串，这使我们能够将数据存储到 localStorage 中。

```
fetch("./phillip.json")
  .then(response => response.json())
  .then(data => {
    localStorage.setItem("profile", JSON.stringify(data))
    localStorage.getItem("profile") // '{"name":"Phillip","age":29,...'
}); 
```

让我们开始规划未来吧。最后，我们希望取出存储的 JSON 字符串，并将其转换回 JSON 对象。JSON.parse() 可以帮助我们做到这一点。

```
...

const profile = localStorage.getItem("profile")
JSON.parse(profile) // {name: "Phillip", age: 29,...} - phillip object! 
```

* * *

## 总结

以下是我们讨论过的要点列表。

*   基于文本的数据格式，用于通过网络传输数据。
*   通常作为对象或字符串。
*   可以在 JSON 方法的帮助下存储在 localStorage 中。
*   JSON.stringiy()将 JSON 对象转换为字符串
*   JSON.parse()将 JSON 字符串转换为对象

感谢您的阅读！！！😄😆希望这篇文章能帮助您了解 JSON 是什么！