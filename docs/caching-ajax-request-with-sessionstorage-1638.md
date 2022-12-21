# 用 SessionStorage 缓存 AJAX 请求

> 原文：<https://dev.to/uriel_hedz/caching-ajax-request-with-sessionstorage-1638>

在某些情况下，缓存 AJAX 请求的响应比每次用户刷新页面时都必须发出相同的请求效果更好。

当您使用以下工具时，这种情况很容易识别:

*   不经常更新的数据。
*   有请求限制的 API，比如 GitHub 公共 API
*   延迟加载不经常改变的元素

在某些情况下，有必要创建一个复杂的系统来保存 AJAX 请求的响应、更新它、终止它、保持它最新等等。但是，在大多数情况下，只需要一个简单的方法来保存它一段时间，进入会话存储。

SessionStorage 是 LocalStorage 不太受欢迎的兄弟，local storage 是 JavaScript Web APIs 家族中最常见的存储兄弟。SessionStorage 与 LocalStorage 有何不同？事实证明，无论您保存到 SessionStorage 中的是什么，都会在用户关闭浏览器后过期，就像其他所有会话一样。

鉴于 SessionStorage 的性质，它是一种在用户关闭浏览器后就会过期的持久性存储，它是在短时间内保存数据的完美 API，而不必担心数据过期，只需将数据保存在那里，等待它过期。

## 使用 SessionStorage

SessionStorage 与 LocalStorage 具有相同的 API，它们之间的唯一区别是它们如何使数据过期，而浏览器只会在需要时从 LocalStorage 中删除数据，SessionStorage 数据会在会话结束时延迟。

对于那些从未使用过 LocalStorage 或 SessionStorage 的人来说，有两种常用的操作，写和读:

```
 //Saves the string Hello world with the key message
  sessionStorage.setItem("message", "Hello world"); 
  // Retrieves the string using the key
  sessionStorage.getItem("message") // Returns "Hello world" 
```

通过这两个操作，我们现在可以缓存来自 AJAX 请求的响应，假设我们必须从 GitHub 检索我们的 repos】

```
function getMyRepos(){
  const endpoint = "https://api.github.com/users/urielhdz/repos" //Replace urielhdz with your own username

  /* get_data */
} 
```

现在，我们将使用本机 fetch 操作从网络中检索数据，以便稍后缓存:

```
function getMyRepos(){
  const endpoint = "https://api.github.com/users/urielhdz/repos" //Replace urielhdz with your own username

  fetch(endpoint).then(r => r.json()); // the json method parses the response into a JSON object
} 
```

为了使代码更具可读性，我们将使用异步函数，但请记住，这并不是本教程的关键部分，它只是使代码更简洁的语法糖:

```
async function getMyRepos(){
  const endpoint = "https://api.github.com/users/urielhdz/repos" //Replace urielhdz with your own username

  const data = await fetch(endpoint).then(r => r.json()); // the json method parses the response into a JSON object
} 
```

现在，数据常量存储来自 GitHub API 的数据响应，有趣的部分来了，缓存响应以避免对同一个 API 的不必要的请求。

```
async function getMyRepos(){
  const endpoint = "https://api.github.com/users/urielhdz/repos" //Replace urielhdz with your own username

  const key = "githubRepos"; // Key to identify our data in sessionStorage

  // First check if there's data in SessionStorage
  let data = sessionStorage.getItem(key);

  if(data){
    // If there's somethin in the sessionStorage with our key, return that data:
    return JSON.parse(data);
  }

  //If there's nothing in the storage, make the AJAX request:
  data = await fetch(endpoint).then(r => r.json());

  //Then save it into the storage to avoid more requests later on:
  sessionStorage.setItem(key, JSON.stringify(data));

  return data;  
} 
```

请记住，LocalStorage 和 SessionStorage 都只能保存字符串，所以为了保存更复杂的结构，比如 JSON 对象，我们必须将其转换为字符串，这意味着当我们检索数据时，我们必须将字符串解析为之前的 JSON 对象。

就是这样，现在我们有了一个方法，只在必要时从 GitHub 请求数据，一旦用户关闭浏览器，数据就会过期，这将允许我们保持数据更新。

## 弊端

正如 Scott Simontis 在评论中指出的，这种使用 SessionStorage 的解决方案存在一些问题:

1.  SessionStorage 是同步的，所以请记住，bot 从存储中写入和读取数据的操作会阻塞应用程序的主线程。
2.  最终用户可以操作存储在 SessionStorage 中的数据，因此您不应该为您的应用程序存储敏感或关键的数据。

感谢 Scott 指出这个问题，正如我在评论中补充的，这个解决方案旨在解决简单和基本的问题，在这些问题上，其他缓存解决方案会显得过于复杂。