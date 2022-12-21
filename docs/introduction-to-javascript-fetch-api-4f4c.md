# JavaScript 获取 API 简介

> 原文：<https://dev.to/attacomsian/introduction-to-javascript-fetch-api-4f4c>

*本文最初发表于[attacomsian.com/blog](https://attacomsian.com/blog/javascript-fetch-api)。*

* * *

Fetch API 是一个基于[承诺的](https://attacomsian.com/blog/promises-javascript) JavaScript API，用于在浏览器中发出异步 HTTP 请求，类似于 XMLHttpRequest (XHR)。与 XHR 不同，它是一个简单而干净的 API，使用 promises 来提供一个更强大、更灵活的功能集来从服务器获取资源。

Fetch 现在已经非常标准化了，除了 IE 之外，所有现代浏览器都支持它。如果需要支持包括 IE 在内的所有浏览器，只需在项目中添加一个 GitHub 发布的 [polyfill](https://github.com/github/fetch) 即可。

## API 用法

使用 Fetch API 真的很简单。只需将 URL(您想要获取的资源的路径)传递给`fetch()`方法:

```
fetch('/js/users.json')
    .then(response => {
        // handle response data
    })
    .catch(err => {
        // handle errors
    }); 
```

我们将想要检索的资源的路径作为参数传递给`fetch()`。它返回一个承诺，当它实现时将响应传递给`then()`。如果请求由于网络故障或任何其他原因而无法完成，那么`catch()`方法会拦截错误。

## 获取请求

默认情况下，Fetch API 对异步请求使用 GET 方法。让我们使用 [Reqres REST API](https://reqres.in) 来检索使用 GET request:
的用户列表

```
fetch('https://reqres.in/api/users')
    .then(res => res.json())
    .then(res => {
        res.data.map(user => {
            console.log(`${user.id}: ${user.first_name}  ${user.last_name}`);
        });
    }); 
```

上述请求在控制台上打印以下内容:

```
1: George Bluth
2: Janet Weaver
3: Emma Wong 
```

调用`fetch()`方法返回一个[承诺](https://attacomsian.com/blog/promises-javascript)。承诺返回的响应是一个流对象，这意味着当我们调用`json()`方法时，它返回另一个承诺。对`json()`方法的调用表明我们正在等待一个 JSON 响应。如果您期待一个 XML 响应，您应该使用`text()`方法。

## 发布请求

就像 [Axios](https://attacomsian.com/blog/axios-javascript) 一样，Fetch 也允许在请求中使用任何其他 HTTP 方法:POST、PUT、DELETE、HEAD 和 OPTIONS。你只需要在`fetch()`选项:
中设置`method`和`body`参数

```
const user = {
    first_name: 'John',
    last_name: 'Lilly',
    job_title: 'Software Engineer'
};

const options = {
    method: 'POST',
    body: JSON.stringify(user),
    headers: {
        'Content-Type': 'application/json'
    }
}

fetch('https://reqres.in/api/users', options)
    .then(res => res.json())
    .then(res => console.log(res)); 
```

Reqres API 向我们发送回带有 ID 和创建时间戳的主体数据:

```
{  "first_name":"John",  "last_name":"Lilly",  "job_title":"Software Engineer",  "id":"482",  "createdAt":"2019-05-12T15:09:13.140Z"  } 
```

## 删除请求

删除请求看起来非常类似于发布请求，除了`body`不是必需的:

```
const options = {
    method: 'DELETE',
    headers: {
        'Content-Type': 'application/json'
    }
}

fetch('https://reqres.in/api/users/2', options)
    .then(res => {
        if (res.ok) {
            return Promise.resolve('User deleted.');
        } else {
            return Promise.reject('An error occurred.');
        }
    })
    .then(res => console.log(res)); 
```

## 错误处理

由于`fetch()`方法返回一个承诺，错误处理很容易。我们可以使用 promise 的`catch()`方法来拦截请求执行过程中抛出的任何错误。但是，如果请求命中服务器并返回，无论服务器返回什么响应，都不会抛出错误。由`fetch()`返回的承诺不会拒绝 HTTP 错误，即使 HTTP 响应代码是 404 或 500。

幸运的是，您可以使用 response 对象的`ok`属性来检查请求是否成功:

```
fetch('https://reqres.in/api/users/22') // 404 Error
    .then(res => {
        if (res.ok) {
            return res.json();
        } else {
            return Promise.reject(res.status);
        }
    })
    .then(res => console.log(res))
    .catch(err => console.log(`Error with message: ${err}`)); 
```

## 响应对象

由`fetch()`方法返回的响应对象包含关于异步调用的请求和响应的信息，包括头、状态代码和状态消息:

```
fetch('https://reqres.in/api/users')
    .then(res => {
        console.log(res.headers.get('content-type'));
        console.log(res.headers.get('expires'));

        console.log(res.status);
        console.log(res.ok); // shorthand for `status` between 200 and 299 
        console.log(res.statusText);
        console.log(res.redirected);
        console.log(res.type);
        console.log(res.url);
    }); 
```

您可以使用几个选项来访问 Fetch API 的响应体:

*   `json()`以 JSON 对象的形式返回正文
*   `text()`以 s 字符串的形式返回正文
*   `blob()`以 Blob 对象的形式返回正文
*   `formData()`以 FormData 对象的形式返回正文
*   `arrayBuffer()`以 arrayBuffer 对象的形式返回正文

所有这些方法都有回报。这里有一个`text()`方法的例子:

```
fetch('https://reqres.in/api/unknown/2')
    .then(res => res.text())
    .then(res => console.log(res)); 
```

上述网络调用的输出将是一个 JSON 字符串:

```
'{"data":{"id":2,"name":"fuchsia rose","year":2001,"color":"#C74375","pantone_value":"17-2031"}}' 
```

## 获取&异步/等待

由于 Fetch 是一个基于承诺的 API，我们可以更进一步，使用最新的 ES2017 [async/await](https://attacomsian.com/blog/async-await-javascript) 语法，使我们的代码更简单，看起来更同步:

```
const fetchUsers = async () => {
    try {
        const res = await fetch('https://reqres.in/api/users');
        if (!res.ok) {
            throw new Error(res.status);
        }
        const data = await res.json();
        console.log(data);
    } catch (error) {
        console.log(error);
    }
}

fetchUsers(); 
```

## 结论

这就是使用 JavaScript Fetch API 的全部内容。与`XMLHttpRequest`相比，这是一个巨大的改进，它有一个易于使用的界面，并且非常适合获取资源(甚至跨网络)。所有现代浏览器都支持 Fetch API，所以不需要使用任何 polyfill，除非你想支持 IE。

* * *

✌️:我写了关于现代 JavaScript、Node.js、Spring Boot 和所有 web 开发的东西。 [**订阅我的简讯**](https://attacomsian.com/newsletter) 每周获取 web 开发教程& protips。

* * *

**喜欢这篇文章？** [在推特上关注@ attacomsian](https://twitter.com/attacomsian)。你也可以在 [LinkedIn](https://linkedin.com/in/attacomsian) 和 [DEV](https://dev.to/attacomsian) 上关注我。