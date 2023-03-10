# 在 JavaScript 中使用本地提取 API 时的错误处理

> 原文：<https://dev.to/paramharrison/error-handling-while-using-native-fetch-api-in-javascript-4bg1>

Fetch API 非常强大。我们可以使用浏览器获取 API 轻松发送 Ajax 请求。但是它也有自己的缺点。

一个主要的缺点是使用 fetch 时的错误处理。

例，

```
fetch(url).then((response) => {
  // Always gets a response, unless there is network error
  // It never throws an error for 4xx or 5xx response 😟
}).catch(error) => {
  // Only network error comes here
}; 
```

*   除非出现网络错误，否则它总是会得到响应
*   所有 4xx、5xx 都不会进入制动块

### 使用承诺获取 API 中的错误处理

首先让我们看看，没有处理错误，

```
fetch(url)
  .then(response => {
    return response.json();
  })
  .then(jsonResponse => {
    // do whatever you want with the JSON response
  }); 
```

*   这很糟糕，因为即使 url 发送了 404，我们也只是将其作为响应发送，而不会破坏它。

我们可以通过抛出错误来纠正它，只允许状态代码在 200 到 299 之间的响应。

```
fetch(url)
  .then((response) => {
    if (response.status >= 200 && response.status <= 299) {
      return response.json();
    } else {
      throw Error(response.statusText);
    }
  })
  .then((jsonResponse) => {
    // do whatever you want with the JSON response
  }).catch(error) => {
    // Handle the error
    console.log(error);
  }; 
```

这将解决问题，您甚至可以提取检查状态部分作为返回承诺或抛出错误的函数。

```
function CheckError(response) {
  if (response.status >= 200 && response.status <= 299) {
    return response.json();
  } else {
    throw Error(response.statusText);
  }
}

// Now call the function inside fetch promise resolver
fetch(url)
  .then(CheckError)
  .then((jsonResponse) => {
  }).catch(error) => {
  }; 
```

### 如何使用 async-await 语法处理提取错误

它和承诺一样，只是语法会改变。首先我们将看到没有错误处理，

```
const response = await fetch(url);
const jsonResponse = await response.json();
console.log(jsonResponse); 
```

错误处理示例，

```
const response = await fetch(url);
if (response.status >= 200 && response.status <= 299) {
  const jsonResponse = await response.json();
  console.log(jsonResponse);
} else {
  // Handle errors
  console.log(response.status, response.statusText);
} 
```

希望你学到了一些使用 fetch API 的技巧，请继续关注更多的 JavaScript 技巧😇