# Dojo 小部件中间件

> 原文：<https://dev.to/odoenet/dojo-widget-middleware-3ok5>

Dojo 6 的最新特性包括新的基于功能的小部件和小部件中间件。

基于类的小部件带有 decorators 来[观察属性变化](https://learn-dojo.com/watch-for-property-changes-in-widgets)并与 [metas](https://learn-dojo.com/dojo-from-the-blocks) 一起工作，这允许你获得关于你的小部件的信息。

随着基于功能的小部件的引入，这些模式已经被新的[中间件](https://dojo.io/learn/middleware/introduction)系统所取代。

## 管理本地状态

有两个中间件可用于管理小部件中的本地状态。

*   [缓存](https://dojo.io/learn/middleware/available-middleware#cache) -保存数据
*   [icache](https://dojo.io/learn/middleware/available-middleware#icache) -工作方式类似于缓存，但在数据改变时也会使小部件失效。

### 缓存

您可能会使用`cache`进行一些细粒度的状态管理，因为如果您确实使用了它，则由您来手动使小部件无效，这样它将使用 [`invalidator`](https://dojo.io/learn/middleware/core-render-middleware#invalidator) 中间件基于更新的`cache`属性进行呈现。

```
// src/widgets/Parrot/Parrot.tsx
import { create, invalidator, tsx } from "@dojo/framework/core/vdom";
import cache from "@dojo/framework/core/middleware/cache";

import * as css from "./Parrot.m.css";

// use `cache` and `invalidator` as middleware
// in render factory
const factory = create({ cache, invalidator });

export const Parrot = factory(function Parrot({
  middleware: { cache, invalidator }
}) {
  const name = cache.get<string>("name") || "";
  return (
    <virtual>
      <h3 classes={[css.root]}>{`Polly: ${name}`}</h3>
      <input
        classes={[css.input]}
        placeholder="Polly want a cracker?"
        type="text"
        onkeyup={event => {
          // update cache data with input value
          cache.set(
            "name",
            (event.target as HTMLInputElement).value
          );
          // invalidate widget to render
          // with new data
          invalidator();
        }}
      />
    </virtual>
  );
});

export default Parrot; 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里看到这个演示。

[https://codesandbox.io/embed/dojo-middleware-cache-3wc5n](https://codesandbox.io/embed/dojo-middleware-cache-3wc5n)

这很好，*但是可以更容易些*。

### icache

`icache`是专门设计来像`cache`一样工作的，但是也在每次更新时运行一个`invalidator()`来用更新的数据呈现小部件。它还附带了一个额外的方法，`icache.getOrSet()`,这个方法将返回当前值或者一个指定的缺省值(如果没有的话)。

```
// src/widgets/Parrot/Parrot.tsx
import { create, tsx } from "@dojo/framework/core/vdom";
import icache from "@dojo/framework/core/middleware/icache";

import * as css from "./Parrot.m.css";

const factory = create({ icache });

export const Parrot = factory(function Parrot({ middleware: { icache } }) {
  // get the current name value or an empty string
  const name = icache.getOrSet("name", "");
  return (
    <virtual>
      <h3 classes={[css.root]}>{`Polly: ${name}`}</h3>
      <input
        classes={[css.input]}
        placeholder="Polly want a cracker?"
        type="text"
        onkeyup={event => {
          // when the cache is updated, it will
          // handle calling the invalidator
          icache.set(
            "name",
            (event.target as HTMLInputElement).value
          );
        }}
      />
    </virtual>
  );
});

export default Parrot; 
```

Enter fullscreen mode Exit fullscreen mode

这相当于您可以在基于类的小部件中使用的 [`@watch`](https://github.com/dojo/framework/tree/master/src/core#internal-widget-state) 装饰器。我猜 99%的情况下，您会使用`icache`来管理小部件中的本地状态。

[https://codesandbox.io/embed/dojo-middleware-icache-n6ktf](https://codesandbox.io/embed/dojo-middleware-icache-n6ktf)

## 应用商店

在 Dojo 中，有很多方法可以使用[商店](https://dojo.io/learn/stores/introduction)。你可以使用[集装箱](https://learn-dojo.com/dojo-containers)或者[供应商](https://github.com/dojo/framework/tree/master/src/stores#advanced)。*或者*，你可以使用[存储](https://dojo.io/learn/stores/introduction#store-middleware)中间件！

我们可以创建一个`store`中间件来保存用户列表。

```
// src/middleware/store.ts
import createStoreMiddleware from "@dojo/framework/core/middleware/store";
import { User } from "../interfaces";

export default createStoreMiddleware<{ users: User[] }>(); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们需要一种方法来检索用户列表。我们可以通过一个[进程](https://github.com/dojo/framework/tree/master/src/stores#processes)来做到这一点，这就是你可以管理应用程序行为的方式。

我们可以构建一个获取一些用户数据的流程。

```
// src/processes/userProcess.ts
import {
  createCommandFactory,
  createProcess
} from "@dojo/framework/stores/process";
import { replace } from "@dojo/framework/stores/state/operations";

const commandFactory = createCommandFactory();

const fetchUsersCommand = commandFactory(async ({ path }) => {
  const response = await fetch("https://reqres.in/api/users");
  const json = await response.json();
  return [replace(path("users"), json.data)];
});

export const getUsersProcess = createProcess("fetch-users", [
  fetchUsersCommand
]); 
```

Enter fullscreen mode Exit fullscreen mode

有了准备好的`store`和`process`，我们可以在一个小部件中使用它们来显示我们的用户列表。

```
// src/widgets/Users/Users.tsx
import { create, tsx } from "@dojo/framework/core/vdom";

import * as css from "./Users.m.css";

import store from "../../middleware/store";
import { fetchUsersProcess } from "../../processes/userProcesses";
import { User } from "../../interfaces";

// pass store to render factory
// as middleware
const render = create({ store });

// helper method to render list of Users
const userList = (users: User[]) =>
  users.map(user => (
    <li key={user.id} classes={[css.item]}>
      <img
        classes={[css.image]}
        alt={`${user.first_name}  ${user.last_name}`}
        src={user.avatar}
      />
      <span classes={[css.title]}>
        {user.last_name}, {user.first_name}
      </span>
    </li>
  ));

export default render(function Users({ middleware: { store } }) {
  // extract helper methods from the store in widget
  const { get, path, executor } = store;
  // get current value of Users
  const users = get(path("users"));
  if (!users) {
    // if no Users, run the `executor` against
    // the process to fetch a list of Users
    executor(fetchUsersProcess)(null);
    // since the process to fetch Users does not need
    // any arguments, execute with null

    // if the network is slow, return
    // a loading message
    return <em>Loading users...</em>;
  }

  return (
    <div classes={[css.root]}>
      <h1>Users</h1>
      <ul classes={[css.list]}>{userList(users)}</ul>
    </div>
  );
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里的关键是`store`中间件有一个`executor`方法，可以用来直接从您的小部件执行流程。

```
executor(fetchUsersProcess)(null); 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，`fetchUsersProcess`不需要有效载荷，所以我们可以将`null`传递给它。例如，如果它需要分页，我们可以传递我们想要的页面作为参数，并在我们的过程中使用它。

你可以在这里看到这个演示。

[https://codesandbox.io/embed/dojo-function-based-widgets-94eyy](https://codesandbox.io/embed/dojo-function-based-widgets-94eyy)

## 总结

还有更多可用的中间件[我们在这篇文章中没有涉及到，涉及到主题化、i18n、DOM 以及与 render 方法的交互。我们将在以后的博客文章中讨论这些问题！](https://dojo.io/learn/middleware/available-middleware)

我真的对 Dojo 最新版本中的所有新特性感到兴奋，并且对可用的中间件感到兴奋，甚至对我可以用一个[定制中间件](https://dojo.io/learn/middleware/middleware-fundamentals#creating-middleware)做什么感到兴奋！