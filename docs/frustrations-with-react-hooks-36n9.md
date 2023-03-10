# React 挂钩带来的挫折

> 原文：<https://dev.to/bnevilleoneill/frustrations-with-react-hooks-36n9>

**作者:[保罗·考恩](https://blog.logrocket.com/author/paulcowan/)✏️**

## 钩子解决什么问题？

在我详述我目前对 Hooks 的失望之前，我想郑重声明，在很大程度上，我是 Hooks 的粉丝。

经常听说钩子存在的主要原因是为了替换类组件。可悲的是，React 官方网站的帖子[介绍钩子](https://reactjs.org/docs/hooks-intro.html)的主要标题用这样一个不那么大胆的声明真正低估了钩子:

> 钩子是 React 16.8 中的新增功能。它们允许您使用状态和其他 React 特性，而无需编写类。

这个解释并没有给我很多使用钩子的动力，除了“类并不酷，伙计”！在我看来，钩子允许我们以一种比以前的模式更优雅的方式解决横切关注点，比如[混合](https://reactjs.org/blog/2016/07/13/mixins-considered-harmful.html)、[高阶组件](https://reactjs.org/docs/higher-order-components.html)和[渲染道具](https://reactjs.org/docs/render-props.html)。

像日志和认证这样的功能不是特定于组件的，钩子允许我们将这种类型的可重用行为附加到组件上。

[![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)](https://logrocket.com/signup/)

## 类组件怎么了？

无状态组件接受一些道具并返回一个 React 元素，这个概念有一些美丽而纯粹的东西。这是一个纯粹的功能，因此，副作用免费。

```
export const Heading: React.FC<HeadingProps> = ({ level, className, tabIndex, children, ...rest }) => {
  const Tag = `h${level}` as Taggable;

  return (
    <Tag className={cs(className)} {...rest} tabIndex={tabIndex}>
      {children}
    </Tag>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，副作用的缺乏使得这些无状态组件有点受限，最终，某处的某些东西必须操纵状态。在 React 中，这通常意味着副作用被添加到有状态类组件中。这些类组件通常被称为容器组件，它们执行副作用并将道具传递给这些纯粹的无状态组件函数。

基于类的生命周期事件有几个记录良好的问题。最大的抱怨之一就是你经常要在`componentDidMount`和`componentDidUpdate`中重复逻辑。

```
async componentDidMount() {
  const response = await get(`/users`);
  this.setState({ users: response.data });
};

async componentDidUpdate(prevProps) {
  if (prevProps.resource !== this.props.resource) {
    const response = await get(`/users`);
    this.setState({ users: response.data });
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用 React 有一段时间，您将会遇到这个问题。

使用钩子，这个副作用代码可以在一个地方使用[效果钩子](https://reactjs.org/docs/hooks-effect.html)来处理。

```
const UsersContainer: React.FC = () => {
  const [ users, setUsers ] = useState([]);
  const [ showDetails, setShowDetails ] = useState(false);

 const fetchUsers = async () => {
   const response = await get('/users');
   setUsers(response.data);
 };

 useEffect( () => {
    fetchUsers(users)
  }, [ users ]
 );

 // etc. 
```

Enter fullscreen mode Exit fullscreen mode

挂钩是一个相当大的改进，但是这与我们以前拥有的纯无状态函数相比是一个很大的进步。这让我第一次感到沮丧。

## 这是另一个需要学习的 JavaScript 范例

声明一下，我是一个 49 岁的 React 粉丝。在用疯狂的观察者和计算属性完成一个 [ember](https://emberjs.com/) 应用程序后，单向数据流将永远在我心中占有一席之地。

`useEffect`和 friends 的问题在于，它在 JavaScript 领域的其他地方都不存在。它是不寻常的，有怪癖，对我来说，精通并消除这些怪癖的唯一方法是在现实世界中使用它，并经历一些痛苦。没有使用计数器的教程能让我进入心流。我是一名自由职业者，除了 React，我还使用其他框架，这让我感到[疲劳](https://dev.to/banesag/javascript-fatigue--bhh)。事实上，我需要设置[eslint-plugin-react-hooks](https://www.npmjs.com/package/eslint-plugin-react-hooks)来让我在这个特定的范例中保持正直和狭隘，这让我感到有点警惕。

## 赴地府，背同相依阵

[useEffect](https://reactjs.org/docs/hooks-effect.html) 钩子可以接受一个可选的第二个参数，称为**依赖数组**，它允许你优化 React 何时执行效果回调。React 将通过 [Object.is](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) 对每个值进行比较，以确定是否有任何变化。如果任何元素与上一个渲染周期不同，那么效果将根据新值运行。

这种比较适用于原始的 JavaScript 类型，但是如果其中一个元素是对象或数组，就会出现问题。 [Object.is](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) 将通过引用比较对象和数组，没有办法覆盖这个功能并提供一个自定义的比较器。

通过引用检查对象是一个常见的问题，我可以用下面我遇到的一个问题的简化版本来说明这个问题:

```
const useFetch = (config: ApiOptions) => {
  const  [data, setData] = useState(null);

  useEffect(() => {
    const { url, skip, take } = config;
    const resource = `${url}?$skip=${skip}&take=${take}`;
    axios({ url: resource }).then(response => setData(response.data));
  }, [config]); // <-- will fetch on each render

  return data;
};

const App: React.FC = () => {
  const data = useFetch({ url: "/users", take: 10, skip: 0 });
  return <div>{data.map(d => <div>{d})}</div>;
}; 
```

Enter fullscreen mode Exit fullscreen mode

在**第 14 行**，如果我们不做些什么来确保每次都使用相同的对象，那么每次渲染都会有一个新的对象被传入`useFetch`。在这种情况下，最好检查该对象的字段，而不是对象引用。

我确实理解为什么 React 没有像我们在类似于 [use-deep-object-compare](https://github.com/kentcdodds/use-deep-compare-effect) 的事情上看到的那样进行深度对象比较。如果不小心，您可能会遇到一些严重的性能问题。我似乎经常重新考虑这个问题，而且这个有很多[的修正。您的对象越动态，您开始添加的工作区就越多。](https://github.com/facebook/react/issues/14476#issuecomment-471199055)

有一个 [eslint 插件](https://github.com/facebook/react/tree/master/packages/eslint-plugin-react-hooks)，你真的应该在你选择的文本编辑器中使用[自动修复设置](https://oprea.rocks/blog/automatically-fix-eslint-code-validation-errors-in-visual-studio-code/)来自动应用 eslint 修复。我确实担心任何需要外部插件来检查正确性的新特性。

事实上， [use-deep-object-compare](https://github.com/kentcdodds/use-deep-compare-effect) 、 [use-memo-one](https://github.com/alexreardon/use-memo-one) 和其他的存在证明了这是一个足够普遍的问题，或者至少是一个困惑点。

## React 依赖于钩子的调用顺序

第一批上架的定制钩子是几个使用钩子查询远程 API 的`useFetch`实现。大多数都回避了从事件处理程序调用远程 API 的问题，因为钩子只能从功能组件的开始处调用。

如果我们拥有的数据有分页链接，我们想在用户点击链接时重新运行效果，该怎么办？下面是一个简单的`useFetch`例子:

```
const useFetch = (config: ApiOptions): [User[], boolean] => {
  const [data, setData] = useState<User[]>([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const { skip, take } = config;

    api({ skip, take }).then(response => {
      setData(response);
      setLoading(false);
    });
  }, [config]);

  return [data, loading];
};

const App: React.FC = () => {
  const [currentPage, setCurrentPage] = useState<ApiOptions>({
    take: 10,
    skip: 0
  });

  const  = useFetch(currentPage);

  if (loading) {
    return <div>loading....</div>;
  }

  return (
    <>
      {users.map((u: User) => (
        <div>{u.name}</div>
      ))}
      <ul>
        {[...Array(4).keys()].map((n: number) => (
          <li>
            <button onClick={() => console.log('what do we do now?')}>{n + 1}</button>
          </li>
        ))}
      </ul>
    </>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

在第 23 行，第一次渲染时会调用一次`useFetch`钩子。在第 35–38 行，分页按钮被呈现，但是我们如何从这些按钮的事件处理程序中调用`useFetch`钩子呢？

钩子的规则明确指出:

> 不要在循环、条件或嵌套函数中调用钩子。相反，总是在 React 函数的顶层使用钩子。

每次组件渲染时都需要以相同的顺序调用钩子。有几个原因可以解释为什么会出现这种情况，这篇文章对此做了很好的阐述。

你绝对不能这么做:

```
<button onClick={() => useFetch({ skip: n + 1 * 10, take: 10 })}>
  {n + 1}
</button> 
```

Enter fullscreen mode Exit fullscreen mode

从 even 处理程序调用`useFetch`钩子会破坏钩子的规则，因为你会破坏钩子在每次渲染时被调用的顺序。

## 从钩子返回一个可执行函数

我看到了这个问题的两个解决方案(我喜欢),它们都遵循相同的主题。有 [react-async-hook](https://github.com/slorber/react-async-hook) 从钩子:
返回一个`execute`函数

```
import { useAsyncCallback } from 'react-async-hook';

const AppButton = ({ onClick, children }) => {
  const asyncOnClick = useAsyncCallback(onClick);
  return (
    <button onClick={asyncOnClick.execute} disabled={asyncOnClick.loading}>
      {asyncOnClick.loading ? '...' : children}
    </button>
  );
};

const CreateTodoButton = () => (
  <AppButton
    onClick={async () => {
      await createTodoAPI('new todo text');
    }}
  >
    Create Todo
  </AppButton>
); 
```

Enter fullscreen mode Exit fullscreen mode

对`useAsyncCallback`钩子的调用将返回一个对象，该对象具有预期的加载、错误和结果属性，以及一个我们可以在事件处理程序中调用的`execute`函数。

[react-hooks-async](https://github.com/dai-shi/react-hooks-async) 对它的`useAsyncTask`函数采取了稍微相似的方法。

下面是一个完整的例子，其中有一个`useAsyncTask`的缩小版本:

[https://codesandbox.io/embed/cxowi](https://codesandbox.io/embed/cxowi)

```
const createTask = (func, forceUpdateRef) => {
  const task = {
    start: async (...args) => {
      task.loading = true;
      task.result = null;
      forceUpdateRef.current(func);
      try {
        task.result = await func(...args);
      } catch (e) {
        task.error = e;
      }
      task.loading = false;
      forceUpdateRef.current(func);
    },
    loading: false,
    result: null,
    error: undefined
  };
  return task;
};

export const useAsyncTask = (func) => {
  const forceUpdate = useForceUpdate();
  const forceUpdateRef = useRef(forceUpdate);
  const task = useMemo(() => createTask(func, forceUpdateRef), [func]);

  useEffect(() => {
    forceUpdateRef.current = f => {
      if (f === func) {
        forceUpdate({});
      }
    };
    const cleanup = () => {
      forceUpdateRef.current = () => null;
    };
    return cleanup;
  }, [func, forceUpdate]);

  return useMemo(
    () => ({
      start: task.start,
      loading: task.loading,
      error: task.error,
      result: task.result
    }),
    [task.start, task.loading, task.error, task.result]
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

`createTask`函数用这个接口返回一个任务对象:

```
interface Task {
  start: (...args: any[]) => Promise<void>;
  loading: boolean;
  result: null;
  error: undefined;
} 
```

Enter fullscreen mode Exit fullscreen mode

该任务有我们期望的`loading`、`error`和`result`状态，但它也返回一个`start`函数，我们可以在以后调用。

由`createTask`创建的任务不会触发更新，因此`forceUpdate`和`useAsyncTask`中的`forceUpdateRef`会触发更新。

我们现在有了一个`start`函数，我们可以从一个事件处理程序中调用它，或者至少可以从一个功能组件的起点之外的某个地方调用它。

但是现在我们已经失去了在函数组件第一次运行时调用钩子的能力。幸运的是 [react-hooks-async](https://github.com/dai-shi/react-hooks-async) 附带了一个`useAsyncRun`函数来促进这一点:

```
export const useAsyncRun = (
  asyncTask: ReturnType<typeof useAsyncTask>,
  ...args: any[]
) => {
  const { start } = asyncTask;
  useEffect(() => {
    start(...args);
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [asyncTask.start, ...args]);
  useEffect(() => {
    const cleanup = () => {
      // clean up code here
    };
    return cleanup;
  });
}; 
```

Enter fullscreen mode Exit fullscreen mode

当任何一个`args`参数改变时，将执行`start`函数。

钩子的用法现在看起来是这样的:

```
const App: React.FC = () => {
  const asyncTask = useFetch(initialPage);
  useAsyncRun(asyncTask);

  const { start, loading, result: users } = asyncTask;

  if (loading) {
    return <div>loading....</div>;
  }

  return (
    <>
      {(users || []).map((u: User) => (
        <div>{u.name}</div>
      ))}

      <ul>
        {[...Array(4).keys()].map((n: number) => (
          <li key={n}>
            <button onClick={() => start({ skip: 10 * n, take: 10 })}>
              {n + 1}
            </button>
          </li>
        ))}
      </ul>
    </>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

根据钩子法则，在功能组件开始时调用`useFetch`钩子。`useAsyncRun`函数负责初始调用 API，`start`函数可以在分页按钮的`onClick`处理程序中使用。

钩子现在可以用了，但不幸的是，复杂性增加了。我们还引入了一个让我有点害怕的封闭。

## 结论

我认为这个例子很好地说明了我目前对钩子的失望。

我确实觉得我们正在为一个优雅的结果跳过一些意想不到的障碍，我也确实理解为什么钩子的调用顺序是至关重要的。不幸的是，只有在一个功能组件开始的时候钩子才是可调用的，这是有局限性的，我认为我们仍然需要寻找解决这个问题的方法。`useFetch`解决方案很复杂，钩子也迫使你使用闭包，我有很多使用闭包时意外发生的事情留下的伤疤。

闭包(比如传递给 useEffect 和 useCallback 的闭包)可以捕获旧版本的 props 和状态值。特别是，如果“输入”数组无意中丢失了一个捕获的变量，就会发生这种情况；这可能会令人困惑。

由于代码在闭包中执行而导致的陈旧状态是 Hooks linter 要解决的问题之一。栈溢出有[很多关于`useEffect`和好友中陈旧状态的问题](https://stackoverflow.com/search?q=stale+state+react+hooks)。我似乎花了过多的时间来扭曲各种依赖数组和包装`useCallback`中的函数，以避免陈旧状态或无限的重新呈现。我明白为什么这是必要的，但这感觉很烦人，而且没有什么可以替代仅仅通过一个现实世界的问题来获得你的条纹。

我在帖子的开头提到过，我很支持钩子，但是它们看起来很复杂。在 JavaScript 领域的任何其他地方，你都找不到类似的东西。在一个功能组件的每一次呈现中都可以调用钩子会带来一些常规 mixins 所没有的问题。对 linter 执行模式的需求确实让我警惕，对闭包的需求总是会带来问题。

我很想在这一点上被证明是错误的，所以请在下面的评论中告诉我我是如何弄错的。

* * *

**编者按:**看到这个帖子有问题？你可以在这里找到正确的版本。

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/0abf868fe5ccbed99d71cb8d9e81ed98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6FG5kvEL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2017/03/1d0cd-1s_rmyo6nbrasp-xtvbaxfg.png%3Fresize%3D1200%252C677%26ssl%3D1)

[log rocket](https://logrocket.com/signup/)是一个前端日志工具，让你重放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 动作和状态，LogRocket 还记录控制台日志、JavaScript 错误、stacktraces、带有头+体的网络请求/响应、浏览器元数据、自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[对 React Hooks](https://blog.logrocket.com/frustrations-with-react-hooks/) 的不满首先出现在[博客](https://blog.logrocket.com)上。