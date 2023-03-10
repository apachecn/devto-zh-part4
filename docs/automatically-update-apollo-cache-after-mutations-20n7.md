# 突变后自动更新阿波罗缓存

> 原文：<https://dev.to/aerogear/automatically-update-apollo-cache-after-mutations-20n7>

### 背景

应用程序缓存为开发人员提供了完整的本地状态以及在离线情况下无缝工作的能力。它还允许我们最小化服务器上的负载。然而，随着这种能力的增加，我们继承了增加的复杂性和极限情况，其中我们的应用程序可能不再按预期执行，可能包含过时和不相关的数据。

在 [Apollo Client 2.0](https://www.apollographql.com/docs/react/) 中，开发者可以使用 [InMemoryCache](https://www.apollographql.com/docs/react/advanced/caching/#inmemorycache) 包为他们的客户端提供一个全功能的缓存解决方案。当您不需要复制服务器知道的所有内容时，此缓存可用于本地解析数据或减少对服务器的调用次数。缓存是标准化的，并提供一个 GraphQL API，在这个 API 下存储查询和突变。这是一种对开发人员友好的组织数据的方式。

下图是典型的 Apollo 客户端缓存的高级示意图。

[![Apollo Cache Diagram](img/a00cd8934aa9da492c49ce78cc604e3f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8-y7eOju--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/58ww58lwyz8w9z0gpnw5.png)

在上面的例子中，我们看到 Apollo 客户机从两个地方之一查询或修改数据。根据您如何配置您的客户端，它将首先检查缓存以查看它是否包含您正在或正在寻找的数据，或者它将询问服务器，然后将结果告诉缓存。这是实现脱机应用程序的基础，在脱机应用程序中，您的客户端现在可以在本地查询和修改数据，而无需与服务器交互。

默认情况下，Apollo 客户端试图保持其缓存最新。例如，假设我们的缓存中有一个`getItems`查询，其中包含一个条目列表，每个条目都有一个标题字段。如果我们(通过变异)更新其中一个条目的标题字段，那么 Apollo 将确保我们的`getItems`查询也被更新。这确保了下次我们运行`getItems`(例如，如果你有一个缓慢或不存在的网络连接)时，我们得到最新的信息。

### 问题

Apollo 很好地处理了这个小例子。然而，这是因为阿波罗已经知道我们更新的这个对象。如果它是我们刚刚创建的新对象呢？或者，如果我们删除一个对象，需要在任何地方取消引用它？这些案例并不那么微不足道。当我开始使用 Apollo 客户端和缓存时，我立即注意到了一些与处理这些用例相关的常见问题。让我们探索如何处理这些更复杂的场景，以及为什么在我自己的应用程序中处理这些场景会让我创建一个通用的包，让社区从中受益。

### 手动缓存更新

为了处理基本缓存更新之外的任何事情，Apollo 使我们能够为客户提供定制的更新功能。如果我们使用基本的“ToDo”应用程序示例，并且我们想要将一个新创建的任务添加到我们的缓存中，那么代码将如下所示:

```
apollo.mutate({
  mutation: createTaskMutation,
  variables: item,
  update: (cache, { data }) => {
    try {
      let { allTasks } = cache.readQuery({ query: getTasks });
      allTasks.push(data);
      cache.writeQuery({
        query: getTasks,
        data: {
            'allTasks': allTasks
        }
      });
    } catch (e) {
        // We should always catch here,
        // as the cache may be empty or the query may fail
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

让我们看一下这个例子，解释一下发生了什么。我们有一个名为`createTaskMutation`的(预构建的)变异，我们的`update`函数接受一个`cache`对象以及从我们的变异结果返回的`data`对象。然后，该函数尝试做三件事:

1.  从我们的缓存中读取预构建的`getTasks`查询。
2.  将新的`data`对象推到我们当前的任务列表中
3.  将该查询与现在包含在其中的新数据一起写回缓存。

一旦这个`update`函数运行，我们将把更新的数据保存在本地缓存中。完美的解决方案！不完全是。

### 引入复杂性

现在我们知道了`update`函数如何处理一个简单的例子，我们可以探索一些更现实的东西。当我们引入需要更新的订阅或多个查询时怎么办？

#### 订阅

如果我们的应用程序也订阅了`getTasks`,那么订阅数据很可能会在突变结果之前返回。这意味着我们创造的变异结果可能已经在我们的缓存中了。这立刻使上面的`update`函数变得更加复杂，因为在当前状态下，我们缓存中的`allTasks`对象将包含我们的新任务两次。换句话说，我们需要确保在我们的缓存上执行重复数据删除。

我们需要对`update`函数进行如下修改:

```
apollo.mutate({
  mutation: createTaskMutation,
  variables: item,
  update: (cache, { data }) => {
    try {
      let { allTasks } = cache.readQuery({ query: getTasks });
      if (allTasks) {
        if (!allTasks.find(task => task.id === data.id)) {
          allTasks.push(data);
        }
      } else {
        allTasks = [data];
      }
      cache.writeQuery({
        query: getTasks,
        data: {
            'allTasks': allTasks
        }
      });
    } catch (e) {
        // We should always catch here,
        // as the cache may be empty or the query may fail
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们从缓存中读取数据，但关键的是，在进行任何更改之前，我们会遍历其内容。如果我们没有在缓存中找到突变的结果，那么我们可以安全地添加它。

#### 多个查询

另一个问题是当我们的缓存中有多个查询需要更新时。当几个不同的查询返回相同数据的子集时，会发生这种情况。例如，如果一个名为`userTasks`的不同查询在添加任务时也需要更新，那么代码将类似于下面的内容:

```
apollo.mutate({
  mutation: createTaskMutation,
  variables: item,
  update: (cache, { data }) => {
    try {
      let { allTasks } = cache.readQuery({ query: getTasks });
      if (allTasks) {
        if (!allTasks.find(task => task.id === data.id)) {
          allTasks.push(data);
        }
      } else {
        allTasks = [data];
      }
      cache.writeQuery({
        query: getTasks,
        data: {
            'allTasks': allTasks
        }
      });
      let { userTasks } = cache.readQuery({ query: userTasks });
      if (userTasks) {
        if (!userTasks.find(task => task.id === data.id)) {
          userTasks.push(data);
        }
      } else {
        userTasks = [data];
      }
      cache.writeQuery({
        query: userTasks,
        data: {
            'userTasks': userTasks
        }
      });
    } catch (e) {
        // We should always catch here,
        // as the cache may be empty or the query may fail
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们有两个需要更新的查询，这两个查询都应该包含我们新的 ToDo 项。我们的更新功能现在变得令人难以置信的迟钝，充满了大量的样板代码。请记住，所有这些样板文件都是针对一个突变的。当我们的应用程序包含几个变种，并且这个样板文件需要放在几个不同的地方时，该怎么办呢？

### 开箱即用缓存助手

与其要求这个样板文件每次都包含基本相同的三个步骤*读取、追加、写入*，我认为将逻辑提取到一个单独的助手包中是个好主意。这些助手提供了一种生成变异选项的方法，这种方法与 Apollo 的 mutate API 兼容。这意味着开发人员只需很少的开销就可以获得上面的`update`函数，该函数是为他们希望的任何变异自动构建的。

让我们看看如何使用这些函数中的一个来确保缓存在突变后完全保持最新。首先，我们需要安装软件包:

```
npm i offix-cache 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以像这样在我们的应用程序中使用它:

```
const { createMutationOptions } = require('offix-cache');

const mutationOptions = {
  mutation: createTaskMutation,
  variables: item,
  updateQuery: {
    query: getTasks
  }
};

const generatedOptions = createMutationOptions(mutationOptions);

apollo.mutate(generatedOptions); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，当突变结果返回时，我们需要提供我们想要更新的查询(在本例中是`getTasks`)。然后返回的`generatedOptions`对象包含一个`update`函数，该函数自动执行我们在前面的代码示例中看到的重复数据删除。

进一步扩展一下——如果像我们之前提到的那样，一旦变异结果返回，我们有几个查询需要更新，该怎么办？该 API 还接受一组查询，因此我们可以传递任何需要更新的查询。`mutationOptions`对象看起来如下:

```
const mutationOptions = {
  mutation: createTaskMutation,
  variables: item,
  updateQuery: [
    {
      query: getTasks
    },
    {
      query: userTasks,
      variables: { user : 1 }
    }
  ]
}; 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们将它传递给`createMutationOptions`，我们将有一组变异选项，其中我们的`update`函数将在收到结果时自动更新几个查询，所有这些都是非常低的开销。

在以后的文章中，我还会详细介绍一些其他有用的好特性，比如自动乐观响应生成和订阅助手。现在，如果你想看一些更高级的用例，请看看该项目的 [readme](https://github.com/aerogear/offix/tree/master/packages/offix-cache) 。或者在 [npm](https://www.npmjs.com/package/offix-cache) 上测试一下。