# 如何使用 Axios 发出 HTTP 请求——GET、POST 和并行请求

> 原文：<https://dev.to/ryancal/how-to-use-axios-to-make-http-requests-get-post-and-parallel-requests-3kn9>

几乎所有基于 web 的应用程序都会执行某种 HTTP 请求。在本文中，我们将介绍一个非常流行的 JavaScript 客户端 Axios。Axios 是一个非常易用和方便的 JavaScript 库，可以在 Node.js 中执行 HTTP 请求。Axios 实际上是一个基于 promise 的 HTTP 客户端库，可以在浏览器和 node.js 环境中工作。它基本上提供了一个单一的 API 来处理 XMLHttpRequests 和 node 的 HTTP 接口，使用起来非常方便。以下是你为什么想要使用 Axios 而不是传统的 Fetch API 的一些原因

*   它无缝支持旧的浏览器。
*   有办法设置响应超时和中止请求
*   它有内置的 CSRF 保护
*   允许您跟踪上传进度
*   执行自动 JSON 数据转换
*   在 Node.js 和 Web 浏览器中无缝工作

## 安装

在我们开始使用 Axios 之前，我们首先需要安装它。有多种方法可以做到这一点:

1.  使用 npm:

```
$ npm install axios 
```

2.使用 bower:

```
$ bower install axios 
```

3.使用纱线:

```
yarn add axios 
```

4.使用 unpkg.com 将它包含在您的页面中:

```
<script src="https://unpkg.com/axios/dist/axios.min.js"></script> 
```

5.手动下载:

```
https://github.com/mzabriskie/axios/tree/master/dist 
```

安装完成后，我们将继续执行请求。在下面的例子中，我们将执行对 Calendarific API 的请求。我们正在使用 Calendarific API。[因此，在这里获取您的 API 密钥](https://calendarific.com/api-documentation)。

[calendar fic](https://calendarific.com/)提供一个 JSON API，用于免费获取 200 多个国家的银行、公共、地方、国家、假日和庆典信息。我们将从最常见的 HTTP 方法 GET 和 POST 开始。

```
// Performing a GET request
const requestUrl = 'https://calendarific.com/api/v2/holidays?country=US&amp;year=2018&amp;api\_key=hsy82&amp;type=national';

axios.get('requestUrl)
  .then(function(response){
    console.log(response.data); // ex.: { holidays: ''}
    console.log(response.status); // ex.: 200
  });  

// Performing a POST request
axios.post('/save', { firstName: 'Marlon', lastName: 'Bernardes' })
  .then(function(response){
    console.log('saved successfully')
  }); 
```

此外，为了方便起见，您通常会使用

*   `axios.get()`
*   `axios.post()`

(就像在 jQuery 中，你可以使用`$.get()`和`$.post()`而不是`$.ajax()` ) Axios 提供了所有 HTTP 动词的方法，这些方法不太流行，但仍在使用:

*   `axios.delete()`
*   `axios.put()`
*   `axios.patch()`
*   `axios.options()`

和一个获取请求的 HTTP 头的方法，丢弃请求体:

*   `axios.head()`

## 同时执行多个并行请求

Axios 的另一个不可低估的强大特性是并行执行多个请求的能力，只需向`axios.all`提供一个数组参数。

当所有请求完成时，您将收到一个数组，其中包含的响应对象的顺序与它们被发送的顺序相同。

或者，您可以使用 axios.spread 将数组分布到多个参数中。Spread 是首选，因为处理数组索引可能会产生误导。

在下面的示例中，将使用`axios.all`同时获取英国、美国、加拿大和墨西哥四个国家的假期，并将结果分布在多个变量中。

```
// Requests will be executed in parallel...
axios.all(\[
   // Remember to replace the api\_key with a valid one.
    axios.get('https://calendarific.com/api/v2/holidays?country=US&year=2019&api\_key=hsy82&type=national'), 
    axios.get('hhttps://calendarific.com/api/v2/holidays?country=UK&year=2019&api\_key=hsy82&type=national'),
    axios.get('hhttps://calendarific.com/api/v2/holidays?country=CA&year=2019&api\_key=hsy82&type=national'),
    axios.get('hhttps://calendarific.com/api/v2/holidays?country=MX&year=2019&api\_key=hsy82&type=national')
  \])
  .then(axios.spread(function (usHolidays, ukHolidays, caHolidays, mxHolidays) {
    //... but this callback will be executed only when all requests are complete.
    console.log('United States Holidays in 2019', usHolidays.data);
    console.log('UK in 2019', ukHolidays.data);
    console.log('Canada Holidays in 2019', caHolidays.data);
    console.log('Mexico Holidays in 2019', mxHolidays.data);
  })); 
```

最后，我们将讨论如何使用 Axios 发送自定义标题。为此，您需要提供一个包含标题的对象作为最后一个参数:

```
var config = {
  headers: {'X-My-Custom-Header': 'Header-Value'}
}; 
```

```
axios.get('https://calendarific.com/api/v2/holidays', config);

//or
axios.post('/save', { firstName: 'Marlon' }, config); 
```

[本文最早出现在 Dotlayer 上。](https://dotlayer.com/how-to-use-axios-to-make-http-requests-get-post-and-parallel-requests/)