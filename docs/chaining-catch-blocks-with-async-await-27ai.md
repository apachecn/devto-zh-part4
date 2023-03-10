# 用异步/等待链接 catch 块

> 原文：<https://dev.to/deammer/chaining-catch-blocks-with-async-await-27ai>

当您在大型应用程序中使用 API 时，逐个处理错误可能会变得单调乏味且重复。

考虑将这个 API 调用打包成一个函数:

```
async function getRandomDog() {
  const response = await axios.get('https://dog.ceo/api/breeds/image/random');
  return response;
}

// Display a random dog image
getRandomDog().then(response => {
  const image = document.createElement("img");
  image.setAttribute("src", response.data.message);
  image.setAttribute("width", "400");
  document.body.appendChild(image);
}); 
```

Enter fullscreen mode Exit fullscreen mode

这很好，但是我们可能应该处理错误，以防我们的 API 调用失败:

```
// Display a random dog image
getRandomDog()
  .then(url => {
    const image = document.createElement("img");
    image.setAttribute("src", response.data.message);
    image.setAttribute("width", "400");
    document.body.appendChild(image);
  })
  .catch(error => {
    alert("Unable to find a dog :(");
  }); 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果 API 失败，我们会告诉用户，但我们也希望记录事件，以便我们知道我们的应用程序有问题。在真实的应用程序中，我们可以访问日志服务，但是为了简单起见，我们将使用`console.log` :

```
async function getRandomDog() {
  const response = await axios
    .get("https://dog.ceo/api/breeds/image/random")
    .catch(error => {
      console.log(error);
      return error;
    });
  return response.data.message;
} 
```

Enter fullscreen mode Exit fullscreen mode

假设`logError`工作正常，当我们的 API 失败时，我们应该会得到通知。但是，我们的用户不会再看到警告，因为我们已经发现了错误。那不是很好的用户体验！

我们的第一反应可能是尝试链接`catch`个块，但那是行不通的:

```
const data = await axios
  .get("https://dog.ceo/api/breeds/image/random")
  .catch(error => {
    console.log(error);
  })
  .catch(error => {
    // This block will never get called
    alert("Something went wrong");
  }); 
```

Enter fullscreen mode Exit fullscreen mode

我最喜欢的解决方案是再次抛出错误，以触发代码中新的`catch`块:

```
const data = await axios.get("https://fake.api.com")
  .catch(error => {
    console.log(error);
    throw error;
  })
  .catch(error => {
    alert("Something went wrong");
  }); 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们的 API 调用失败时，我们将把错误记录到我们的报告系统*中，并*通知用户。赢赢！

* * *

如果我们有多个端点，我们还可以更进一步，集中错误报告。例如:

```
function handleAPIError(error) {
  const { status } = error.response;

  switch (status) {
    case 400:
      console.log("Error: invalid request");
      break;
    case 401:
      console.log("Error: not authenticated");
      break;
    case 500:
      console.log("Error: server problems");
      break;
  }

  throw error;
}

async function getRandomDog() {
  const response = await axios
    .get("https://dog.ceo/api/breeds/image/random")
    .catch(handleAPIError);
  return response;
}

async function getRandomCat() {
  const response = await axios
    .get("https://api.thecatapi.com/img/search")
    .catch(handleAPIError);
  return response;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在已经实现了一个错误处理程序，它可以在我们的 API 帮助器中重用。它允许错误过滤到前端，这样我们就可以向用户显示正确的错误消息。

这里有一支笔，里面有无限的猫、狗和错误:

[https://codepen.io/deammer/embed/rEvmNy?height=600&default-tab=js,result&embed-version=2](https://codepen.io/deammer/embed/rEvmNy?height=600&default-tab=js,result&embed-version=2)

* * *

感谢您的阅读！让我知道你的想法，我很想知道你如何处理应用程序中的错误。