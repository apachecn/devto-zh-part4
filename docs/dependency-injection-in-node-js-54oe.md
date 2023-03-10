# Node.js 中的依赖注入

> 原文：<https://dev.to/kbariotis/dependency-injection-in-node-js-54oe>

这篇文章是关于如何使用 [awilix](https://github.com/jeffijoe/awilix) 在 Node.js 中实现[依赖注入](https://en.wikipedia.org/wiki/Dependency_injection)，这个依赖注入容器我个人已经使用了相当一段时间了。我将使用单元测试作为依赖注入有用的用例，但不用说，它的目的远不止于此。

这些年来，我看到了不同的模式，这是一个让我使用依赖注入的总结。Node.js 和 JavaScript，作为一种动态语言，可以原谅不同的技术。我的目标是实现一种标准技术，在我参与的所有项目和团队中使用。

## 单元测试的案例

我们努力为我们的函数编写更多的单元测试。尽管这可能很困难，特别是在函数有副作用的情况下，也就是当它直接与外部世界通信而不是通过它的标准输入或输出时。我们来看看:

```
const client = require('axios');

const externalServiceRoot = 'https://api.example-external-service.com';

async function callExternalService(anArgument) {
  const { response } = await client.post(`${externalServiceRoot}/an/endpoint`, anArgument);

  if (!response.success) {
    throw new Error('Response doesn\'t look good');
  }

  return response.data;
}

module.exports = { callExternalService }; 
```

这里有一个您打算在应用程序中使用的简单模块。可能多次。

它可以使用 [Nock](https://github.com/nock/nock) 进行测试，这是一个网络拦截器，它将位于网卡中，并断言某些连接通过。我假设这不是一个单元测试，而是一个集成测试，因为网络请求实际上会到达网卡(或 node . js ' T0 '模块)。此外，这将使测试慢得多，因为将一直通过`axios`。

我们如何进行单元测试呢？我们用一个实际上发出假请求的对象来模拟`axios`对象。这样，测试将只测试函数内部的代码。会快得多，而且实际上只做预期要做的事情，也就是只测试函数内部的代码。

我们如何在 Node.js 中做到这一点呢？

## 猴子补丁

这些年来我已经看到了这一点，尤其是在 Node.js 的早期，原型继承的时代。`axios`模块将被附加到导出的对象上，这样它就可以被外界看到。使用`_`约定，我们可以表示它是私有的。但是测试将能够修补它并传递一个存根。

这里有一个例子:

```
const axios = require('axios');

function ExternalServiceConstructor() {
  this._client = axios;
}

ExternalServiceConstructor.prototype.callExternalService(anArgument) {
  const { response } = await this._client.post(`${externalServiceRoot}/an/endpoint`, anArgument);

  if (!response.success) {
    throw new Error('Response doesn\'t look good');
  }

  return response.data;
}

module.exports = new ExternalServiceConstructor(); 
```

该模块现在正在导出一个 singleton，你将能够访问`_client`，从而能够存根它。(现在，使用`Class`关键字或`Object.create`更少的样板代码也能达到同样的效果)

我不喜欢这种方法有两个原因。

首先，由于导出的模块是单例的，这意味着如果一个测试 stubss 了`_client`属性，它将在所有其他需要的地方保持 stub。您应该非常小心地重置存根版本，以便其他测试文件可以正常工作。

第二，它暴露了公共属性，这对消费者来说很危险。人们会试着变聪明并改变它。

## 依赖注入

依赖注入并不是一个新概念，在其他语言中也广为人知，在这些语言中，猴子补丁并不真正可行。在这种情况下，您负责在运行时传递模块的任何依赖关系。这是我们的第一个例子，在函数的签名中注入了`axios`模块:

```
const externalServiceRoot = 'https://api.example-external-service.com';

async function callExternalService(client, anArgument) {
  const { response } = await client.post(`${externalServiceRoot}/an/endpoint`, anArgument);

  if (!response.success) {
    throw new Error('Response doesn\'t look good');
  }

  return response.data;
}

module.exports = { callExternalService }; 
```

现在，每当消费者调用`callExternalService`函数时，他负责传递模块。这可能会很痛苦，而且不能提供最好的开发人员体验。首先，函数签名因混合参数而膨胀，其次，您可能不得不一直这样做，这会造成冗余和重复。

以下是我自己见过和用过的一些其他模式。

### 功能工厂

使用函数式编程中的 currying 来创建一个工厂，该工厂将生产我们的最终函数。

```
const externalServiceRoot = 'https://api.example-external-service.com';

function makeCallExternalService(client) {
  return async function callExternalService(anArgument) {
    const { response } = await client.post(`${externalServiceRoot}/an/endpoint`, anArgument);

    if (!response.success) {
      throw new Error('Response doesn\'t look good');
    }

    return response.data;
  }
}

module.exports = { makeCallExternalService }; 
```

用客户端调用`makeCallExternalService`，你就有了你的功能。

### 依赖关系总是作为第一个自变量

有一个惯例，总是将所有依赖项作为函数的第一个参数传递。

```
const externalServiceRoot = 'https://api.example-external-service.com';

async function callExternalService(dependencies, anArgument) {
  const { response } = await dependencies.client.post(`${externalServiceRoot}/an/endpoint`, anArgument);

  if (!response.success) {
    throw new Error('Response doesn\'t look good');
  }

  return response.data;
}

module.exports = { callExternalService }; 
```

## 依赖注入容器

依赖注入容器就像你所想的那样。它包含了你所有的依赖项。它的职责是构造你所有的类或模块，从而从你的业务逻辑中抽象出来。

此外，它还处理模块的布线。相反，我们不是直接要求我们的`externalService`模块，而是从容器中请求它，它将确保返回所需的函数及其依赖项。

在 Node.js 中，我只用过一个这样的容器，真的没有回头看。这是一个容器。

awilix 真的很强大，一旦你开始使用它，它真的可以让你远离大部分依赖注入的麻烦。

我们将很快看到一个例子，但在此之前，让我们向我们的`externalService`模块添加另一个次要要求。让我们要求我们也要注入`externalServiceRoot`变量，因为我们希望基于我们的应用程序运行的环境达到一个不同的端点。生产/筹备和本地开发是不同的。依赖项注入不能仅用于清除依赖项。

下面是使用普通依赖注入的最后一个函数。

```
async function callExternalService(client, externalServiceRoot, anArgument) {
  const { response } = await client.post(`${externalServiceRoot}/an/endpoint`, anArgument);

  if (!response.success) {
    throw new Error('Response doesn\'t look good');
  }

  return response.data;
}

module.exports = { callExternalService }; 
```

没错。现在签名真的开始变得臃肿了。让我们从 awilix 开始。我们将从输入来自环境变量的配置值开始。

```
const { createContainer, asValue } = require('awilix');

const container = createContainer();

container.register({
  externalServiceRoot: asValue(process.env.EXTERNAL_SERVICE_ROOT)
}); 
```

很好，现在我们的容器知道配置值了。`asValue`只要有人需要`externalServiceRoot`变量，就简单地指示 awilix 按原样传递该值。

不过，我个人喜欢做的是在这里添加一个额外的防护，以防止我们的应用程序在没有该值的情况下继续运行。您可以使用配置管理器来处理这个问题，但是现在让我们做一些更改。

```
const { createContainer, asFunction } = require('awilix');

const container = createContainer();

container.register({
  externalServiceRoot: asFunction(() => {
    if (!process.env.EXTERNAL_SERVICE_ROOT) {
      throw new Error('EXTERNAL_SERVICE_ROOT is not defined.')
    }

    return process.env.EXTERNAL_SERVICE_ROOT;
  }
}); 
```

现在`asFunction`有一点不同，因为只要有人需要这个变量，它就会运行这个函数。

太好了。这是我们对`callExternalService`函数的第一个依赖。现在我们需要通过`axios`模块。在理想的情况下，您实际上会传递一个遵循特定接口的模块，否则您会将您的代码耦合到`axios`,使得以后切换到另一个 HTTP 客户端库更加困难。现在，让我们假设`axios`遵循我们的标准接口。

```
const { createContainer, asFunction, asValue } = require('awilix');
const axios = require('axios');

const container = createContainer();

container.register({
  client: asValue(axios)
});

container.register({
  externalServiceRoot: asFunction(() => {
    if (!process.env.EXTERNAL_SERVICE_ROOT) {
      throw new Error('EXTERNAL_SERVICE_ROOT is not defined.')
    }

    return process.env.EXTERNAL_SERVICE_ROOT;
  }
}); 
```

现在我们的容器也知道我们的 HTTP 客户端了。是时候把所有东西放在一起了。我们将使用上面的函数工厂。

```
const { createContainer, asFunction, asValue } = require('awilix');
const axios = require('axios');

const container = createContainer();

// The container will be passed to this function with
// everything is contained. awilix is smart enough to
// understand what exactly you are requiring.
function makeCallExternalService({ client, externalServiceRoot }) {
  return async function callExternalService(anArgument) {
    const { response } = await client.post(`${externalServiceRoot}/an/endpoint`, anArgument);

    if (!response.success) {
      throw new Error('Response doesn\'t look good');
    }

    return response.data;
  }
}

container.register({
  callExternalService: asFunction(makeCallExternalService)
})

container.register({
  client: asValue(axios)
});

container.register({
  externalServiceRoot: asFunction(() => {
    if (!process.env.EXTERNAL_SERVICE_ROOT) {
      throw new Error('EXTERNAL_SERVICE_ROOT is not defined.')
    }

    return process.env.EXTERNAL_SERVICE_ROOT;
  }
});

module.exports = container; 
```

真好。我们创建了将容器作为输入的工厂，因此我们可以请求迄今为止传递给它的任何值，并生成我们希望注入这些值的函数。

当我们将工厂`makeCallExternalService`传递给 awilix 时，它知道运行函数并将其返回值传递给需要它的任何人。返回值将是我们的最终函数及其所有依赖项。

现在让我们试着使用这个函数。

```
// require the container from above
const container = require('./container');

// Will fail if the environment doesn't contain
// the EXTERNAL_SERVICE_ROOT variable
const callExternalService = container.resolve('callExternalService');

// Call the function that was produced from the
// makeCallExternalService function factory
callExternalService('argument') 
```

太神奇了。一切都是有线的，并由容器自己处理。我们可以在我们的应用中使用这个函数，也可以用`nock`进行集成测试。但同样令人惊奇的是，我们的函数现在可以针对存根进行测试。就这么办吧。

```
// -- src/callExternalService.js

// We have moved our function to another file

function makeCallExternalService({ client, externalServiceRoot }) {
  return async function callExternalService(anArgument) {
    const { response } = await client.post(`${externalServiceRoot}/an/endpoint`, anArgument);

    if (!response.success) {
      throw new Error('Response doesn\'t look good');
    }

    return response.data;
  }
}

module.exports = { makeCallExternalService };

// -- callExternalService.test.js

// This is our unit test

// This time we require our function directly,
// not through the container
const { makeCallExternalService } = require('./callExternalService')

// HTTP Client stub
const client = {
  put: Promise.resolve({ response: { data: { success: false } } });
}

// Produce the function under test
// by passing our stubs
const callExternalService = makeCallExternalService({
  externalServiceRoot: 'FAKE_ROOT',
  client,
})

describe('callExternalService', () => {
  context('with false success response', () => {
    it('should throw', () => {
      expect(() => callExternalService('argument')).to.throw('Error');
    })
  })
}) 
```

这就是了。用存根单元测试我们的函数。我们的容器装载了我们可以在应用中使用的依赖项和模块。我们不必担心将模块连接在一起。每当我们编写一个新的模块，我们都知道在哪里可以找到任何依赖，如何请求它们，我们所关注的是手头的任务。

## 结论

我建议你看一下[的库](https://github.com/jeffijoe/awilix)，浏览一下它的文档和例子。另外，我发现这个 [API 样板文件](https://github.com/talyssonoc/node-api-boilerplate)使用了 awilix，也利用了许多最佳实践。即使你不打算照原样使用它，这也是一本很好的读物。

上面是一个过于简单的例子，说明了如何在 Node.js 上实现 awilix 的依赖注入。实际上，随着项目的增长，会出现其他的复杂情况，比如容器自身膨胀。awilix 是一个了不起的库，并且已经解决了大部分问题。

我很想知道你们是如何在项目中处理依赖注入的。请在下方留言评论。

这是我博客上的一篇交叉文章。点击找到[原文。](https://kostasbariotis.com/dependency-injection-in-node-js/)