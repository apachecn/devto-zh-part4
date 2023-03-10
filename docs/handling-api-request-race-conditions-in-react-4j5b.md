# 在 React 中处理 API 请求竞争条件

> 原文：<https://dev.to/sebastienlorber/handling-api-request-race-conditions-in-react-4j5b>

**注意**:这最好在[我的网站](https://sebastienlorber.com/handling-api-request-race-conditions-in-react)上阅读。最初的帖子包括我不得不删除的 runnable React 演示，因为 dev.to 不支持 MDX。

这也是我在这里的第一个帖子，希望你会喜欢:)

* * *

许多博客文章谈到在 React 应用程序中加载 API/异步数据，有`componentDidMount`、`useEffect`、Redux、Apollo...

然而，所有这些文章都是普遍乐观的，从来没有提到一些需要考虑的重要问题:竞争情况可能会发生，你的用户界面可能会以不一致的状态结束(T2 状态)。

一幅图像胜过千言万语:

[![](img/a23d59bda052bf84f6f3132906488948.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mcpZvy0H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sebastienlorber.com/static/14ad380d2d7549d8527781650c272e77/02721/trump-macron.webp)

你搜索 **Macron** ，然后改变主意搜索 **Trump** ，你最终得到的是**你想要的(Trump)** 和**你得到的(Macron)** 之间的不匹配。

如果你的用户界面很有可能处于这种状态，那么你的应用就会受到竞争条件的影响。

## 为什么会出现这种情况？

有时，多个请求并行发出(竞争呈现同一个视图)，我们只是假设最后一个请求将最后解决。实际上，最后一个请求可能首先解析，或者失败，导致第一个请求最后解析。

这比你想象的要经常发生。对于一些应用程序来说，这会导致非常严重的问题，比如**用户买错了产品**，或者**医生给病人开错了药**。

原因的非详尽列表:

*   网络速度慢、质量差、不可预测，并且请求延迟可变...
*   在拒绝服务攻击下，后端负载过重，限制了一些请求...
*   用户在乡村快速点击、通勤、旅行...
*   你只是运气不好

开发者在开发中是看不到的，那里的网络条件一般都不错，有时候在你自己的电脑上运行后端 API，延迟接近 0ms。

在本帖中，我将使用真实的网络模拟和可运行的演示向您展示这些问题是如何解决的。我还将解释如何修复这些问题，这取决于您已经使用的库。

**免责声明**:为了保持对竞争条件的关注，如果您在卸载后`setState`，下面的代码示例不会阻止 React 警告。

## 入罪代码:

您可能已经阅读了包含以下代码的教程:

```
const StarwarsHero = ({ id }) => {
  const [data, setData] = useState(null);

  useEffect(() => {
    setData(null);

    fetchStarwarsHeroData(id).then(
      result => setData(result),
      e => console.warn('fetch failure', e),
    );
  }, [id]);

  return <div>{data ? data.name : <Spinner />}</div>;
}; 
```

Enter fullscreen mode Exit fullscreen mode

或者用类 API:

```
class StarwarsHero extends React.Component {
  state = { data: null };

  fetchData = id => {
    fetchStarwarsHeroData(id).then(
      result => setState({ data: result }),
      e => console.warn('fetch failure', e),
    );
  };

  componentDidMount() {
    this.fetchData(this.props.id);
  }

  componentDidUpdate(nextProps) {
    if (nextProps.id !== this.props.id) {
      this.fetchData(this.props.id);
    }
  }

  render() {
    const { data } = this.state;
    return <div>{data ? data.name : <Spinner />}</div>;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

以上两个版本都导致了同样的结果。当快速更改 id 时，即使您有良好的家庭网络和非常快的 API，也会出现问题，有时会呈现以前请求的数据。请不要以为退跳保护了你:它只是减少了倒霉的几率。

现在让我们看看当你在一列有几个隧道的火车上会发生什么。

## 模拟恶劣的网络条件

让我们构建一些实用程序来模拟恶劣的网络条件:

```
import { sample } from 'lodash';

// Will return a promise delayed by a random amount, picked in the delay array
const delayRandomly = () => {
  const timeout = sample([0, 200, 500, 700, 1000, 3000]);
  return new Promise(resolve =>
    setTimeout(resolve, timeout),
  );
};

// Will throw randomly with a 1/4 chance ratio
const throwRandomly = () => {
  const shouldThrow = sample([true, false, false, false]);
  if (shouldThrow) {
    throw new Error('simulated async failure');
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 添加网络延迟

您的网络可能很慢，或者后端可能需要时间来响应。

```
useEffect(() => {
  setData(null);

  fetchStarwarsHeroData(id)
    .then(async data => {
      await delayRandomly();
      return data;
    })
    .then(
      result => setData(result),
      e => console.warn('fetch failure', e),
    );
}, [id]); 
```

Enter fullscreen mode Exit fullscreen mode

### 添加网络延迟+故障

你在乡下的火车上，有一些隧道:请求被随机延迟，其中一些可能会失败。

```
useEffect(() => {
  setData(null);

  fetchStarwarsHeroData(id)
    .then(async data => {
      await delayRandomly();
      throwRandomly();
      return data;
    })
    .then(
      result => setData(result),
      e => console.warn('fetch failure', e),
    );
}, [id]); 
```

Enter fullscreen mode Exit fullscreen mode

这段代码很容易导致奇怪的、不一致的 UI 状态。

## 如何避免这个问题

让我们假设 3 个请求 R1、R2 和 R3 按此顺序被触发，并且仍然处于挂起状态。解决方案是只处理来自 R3 的响应，即最后发出的请求。

有几种方法可以做到这一点:

*   忽略来自以前 api 调用的响应
*   取消以前的 api 调用
*   取消和忽略

### 忽略以前 api 调用的响应

下面是一个可能的实现。

```
// A ref to store the last issued pending request
const lastPromise = useRef();

useEffect(() => {
  setData(null);

  // fire the api request
  const currentPromise = fetchStarwarsHeroData(id).then(
    async data => {
      await delayRandomly();
      throwRandomly();
      return data;
    },
  );

  // store the promise to the ref
  lastPromise.current = currentPromise;

  // handle the result with filtering
  currentPromise.then(
    result => {
      if (currentPromise === lastPromise.current) {
        setData(result);
      }
    },
    e => {
      if (currentPromise === lastPromise.current) {
        console.warn('fetch failure', e);
      }
    },
  );
}, [id]); 
```

Enter fullscreen mode Exit fullscreen mode

有些人可能会尝试使用`id`来进行过滤，但这不是一个好主意:如果用户点击`next`然后点击`previous`，我们可能会对同一个英雄有两个不同的请求。一般来说，这不是问题(因为两个请求通常会返回完全相同的数据)，但是使用 promise identity 是一个更通用和可移植的解决方案。

### 取消以前的 api 调用

最好在运行中取消以前的 api 请求:浏览器可以避免解析响应，并防止一些无用的 CPU/网络使用。`fetch`支持取消感谢`AbortSignal` :

```
const abortController = new AbortController();

// fire the request, with an abort signal,
// which will permit premature abortion
fetch(`https://swapi.co/api/people/${id}/`, {
  signal: abortController.signal,
});

// abort the request in-flight
// the request will be marked as "cancelled" in devtools
abortController.abort(); 
```

Enter fullscreen mode Exit fullscreen mode

中止信号就像一个小的事件发射器，你可以触发它(通过`AbortController`)，每个以这个信号开始的请求都会被通知和取消。

让我们看看如何使用这个特性来解决竞争条件:

```
// Store abort controller which will permit to abort
// the last issued request
const lastAbortController = useRef();

useEffect(() => {
  setData(null);

  // When a new request is going to be issued,
  // the first thing to do is cancel the previous request
  if (lastAbortController.current) {
    lastAbortController.current.abort();
  }

  // Create new AbortController for the new request and store it in the ref
  const currentAbortController = new AbortController();
  lastAbortController.current = currentAbortController;

  // Issue the new request, that may eventually be aborted
  // by a subsequent request
  const currentPromise = fetchStarwarsHeroData(id, {
    signal: currentAbortController.signal,
  }).then(async data => {
    await delayRandomly();
    throwRandomly();
    return data;
  });

  currentPromise.then(
    result => setData(result),
    e => console.warn('fetch failure', e),
  );
}, [id]); 
```

Enter fullscreen mode Exit fullscreen mode

这段代码一开始看起来不错，但实际上我们仍然不安全。

让我们考虑下面的代码:

```
const abortController = new AbortController();

fetch('/', { signal: abortController.signal }).then(
  async response => {
    await delayRandomly();
    throwRandomly();
    return response.json();
  },
); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们在获取过程中中止请求，浏览器将得到通知并采取相应措施。但是如果流产发生在浏览器正在运行`then()`回调的时候，它没有办法处理这部分代码的流产，你得自己写这个逻辑。如果流产发生在我们添加的假延迟期间，它不会取消该延迟并停止流动。

```
fetch('/', { signal: abortController.signal }).then(
  async response => {
    await delayRandomly();
    throwRandomly();
    const data = await response.json();

    // Here you can decide to handle the abortion the way you want.
    // Throwing or never resolving are valid options
    if (abortController.signal.aborted) {
      return new Promise();
    }

    return data;
  },
); 
```

Enter fullscreen mode Exit fullscreen mode

让我们回到我们的问题上来。这是最后一个安全的版本，中途中止请求，但也使用中止来最终过滤结果。同样，让我们使用钩子清理函数，正如我在 Twitter 上被建议的那样[，这使得代码稍微简单了一些。](https://twitter.com/BenMcMahen/status/1167529036295233536) 

```
useEffect(() => {
  setData(null);

  // Create the current request's abort controller
  const abortController = new AbortController();

  // Issue the request
  fetchStarwarsHeroData(id, {
    signal: abortController.signal,
  })
    // Simulate some delay/errors
    .then(async data => {
      await delayRandomly();
      throwRandomly();
      return data;
    })
    // Set the result, if not aborted
    .then(
      result => {
        // IMPORTANT: we still need to filter the results here,
        // in case abortion happens during the delay.
        // In real apps, abortion could happen when you are parsing the json,
        // with code like "fetch().then(res => res.json())"
        // but also any other async then() you execute after the fetch
        if (abortController.signal.aborted) {
          return;
        }
        setData(result);
      },
      e => console.warn('fetch failure', e),
    );

  // Trigger the abortion in useEffect's cleanup function
  return () => {
    abortController.abort();
  };
}, [id]); 
```

Enter fullscreen mode Exit fullscreen mode

现在只有我们是安全的。

## 使用库

手动完成所有这些既复杂又容易出错。希望一些图书馆能为你解决这个问题。让我们探索一下通常用于将数据加载到 React 中的库的非详尽列表。

### Redux

有多种方法可以将数据加载到 Redux 存储中。一般如果你用的是 Redux-saga 或者 Redux-observable 就可以了。对于 Redux-thunk、Redux-promise 和其他中间件，您可以查看下一节中的“普通反应/承诺”解决方案。

#### [T1】Redux-saga](#reduxsaga)

您可能会注意到 Redux-saga API 上有多个`take`方法，但通常您会发现许多使用 [`takeLatest`](https://redux-saga.js.org/docs/api/) 的示例。这是因为`takeLatest`会保护你免受那些比赛条件的影响。

```
Forks a saga on each action dispatched to the Store
that matches pattern. And automatically cancels any previous saga
task started previously if it's still running. 
```

Enter fullscreen mode Exit fullscreen mode

```
function* loadStarwarsHeroSaga() {
  yield* takeLatest(
    'LOAD_STARWARS_HERO',
    function* loadStarwarsHero({ payload }) {
      try {
        const hero = yield call(fetchStarwarsHero, [
          payload.id,
        ]);
        yield put({
          type: 'LOAD_STARWARS_HERO_SUCCESS',
          hero,
        });
      } catch (err) {
        yield put({
          type: 'LOAD_STARWARS_HERO_FAILURE',
          err,
        });
      }
    },
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

先前的`loadStarwarsHero`发电机执行将被“取消”。不幸的是，潜在的 API 请求不会真的被取消(为此你需要一个`AbortSignal`),但 Redux-saga 将确保成功/错误动作将只被分派给 Redux，用于最后请求的《星球大战》英雄。对于飞行中的请求取消，遵循[这一问题](https://github.com/redux-saga/redux-saga/issues/1917)

您也可以选择退出此保护，使用`take`或`takeEvery`。

#### Redux-可观察

同样，Redux-observable(其实是 RxJS)也有解: [`switchMap`](https://www.learnrxjs.io/operators/transformation/switchmap.html) :

```
The main difference between switchMap and other flattening operators
is the cancelling effect. On each emission the previous inner observable
(the result of the function you supplied) is cancelled and
the new observable is subscribed. You can remember this
by the phrase switch to a new observable. 
```

Enter fullscreen mode Exit fullscreen mode

```
const loadStarwarsHeroEpic = action$ =>
  action$.ofType('LOAD_STARWARS_HERO').switchMap(action =>
    Observable.ajax(`http://data.com/${action.payload.id}`)
      .map(hero => ({
        type: 'LOAD_STARWARS_HERO_SUCCESS',
        hero,
      }))
      .catch(err =>
        Observable.of({
          type: 'LOAD_STARWARS_HERO_FAILURE',
          err,
        }),
      ),
  ); 
```

Enter fullscreen mode Exit fullscreen mode

如果你知道自己在做什么，你也可以使用其他的 RxJS 操作符，比如`mergeMap`，但是很多教程都会使用`switchMap`，因为这是一个更安全的默认设置。像 Redux-saga 一样，它不会在运行中取消底层请求，但有[解决方案](https://github.com/ReactiveX/rxjs/issues/3122)来添加这种行为。

### 阿波罗

Apollo 允许您传递 GraphQL 查询变量。每当星球大战英雄 id 改变时，就会发出一个新的请求来加载适当的数据。你可以使用特设，渲染道具或钩子，阿波罗将永远保证，如果你请求`id: 2`，你的用户界面永远不会返回你另一个星球大战英雄的数据。

```
const data = useQuery(GET_STARWARS_HERO, {
  variables: { id },
});

if (data) {
  // This is always true, hopefully!
  assert(data.id === id);
} 
```

Enter fullscreen mode Exit fullscreen mode

### 香草有什么反应

有许多库可以将数据加载到 React 组件中，而不需要全局状态管理解决方案。

我创建了 [react-async-hook](https://github.com/slorber/react-async-hook) :一个非常简单小巧的钩子库，用于将异步数据加载到 react 组件中。它有非常好的本机类型脚本支持，并通过使用上面讨论的技术保护您免受竞争条件的影响。

```
import { useAsync } from 'react-async-hook';

const fetchStarwarsHero = async id =>
  (await fetch(
    `https://swapi.co/api/people/${id}/`,
  )).json();

const StarwarsHero = ({ id }) => {
  const asyncHero = useAsync(fetchStarwarsHero, [id]);
  return (
    <div>
      {asyncHero.loading && <div>Loading</div>}
      {asyncHero.error && (
        <div>Error: {asyncHero.error.message}</div>
      )}
      {asyncHero.result && (
        <div>
          <div>Success!</div>
          <div>Name: {asyncHero.result.name}</div>
        </div>
      )}
    </div>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

保护您的其他选项:

*   [react-async](https://github.com/ghengeveld/react-async) :非常类似，也是渲染道具 api
*   [react-refetch](https://github.com/heroku/react-refetch) :基于 HOCs 的旧项目

还有许多其他的库选项，我不能告诉你它们是否在保护你:看看实现。

**注意**:未来几个月`react-async-hook`和`react-async`有可能合并。

**注意:**:可以使用`StarwarsHero key={id} id={id}/>`作为一个简单的 React 解决方法，以确保组件在每次 id 改变时重新挂载。这将保护您(有时是一个有用的特性)，但是会增加您的工作量。

### 香草的承诺和 Javascript

如果你正在处理普通的承诺和 Javascript，这里有一些简单的工具可以用来防止这些问题。

如果您在 Redux 中使用 thunks 或 promises，那么这些工具对于处理竞争情况也很有用。

**注意**:其中一些工具实际上是 [react-async-hook](https://github.com/slorber/react-async-hook) 的底层实现细节。

#### 可撤销的承诺

React 有一篇旧的博客文章 [isMounted()是一个反模式](https://reactjs.org/blog/2015/12/16/ismounted-antipattern.html)，在这个反模式上，您将学习如何使一个承诺可取消，以避免卸载后的 setState 警告。承诺并不是真的`cancellable`(底层 api 调用不会被取消)，但是你可以选择忽略或者拒绝一个承诺的响应。

我做了一个库[棒极了——命令式——承诺](https://github.com/slorber/awesome-imperative-promise)让这个过程变得更容易:

```
import { createImperativePromise } from 'awesome-imperative-promise';

const id = 1;

const { promise, resolve, reject, cancel } = createImperativePromise(fetchStarwarsHero(id);

// will make the returned promise resolved manually
resolve({
  id,
  name: "R2D2"
});

// will make the returned promise rejected manually
reject(new Error("can't load Starwars hero"));

// will ensure the returned promise never resolves or reject
cancel(); 
```

Enter fullscreen mode Exit fullscreen mode

**注意**:所有这些方法都必须在底层 API 请求解析或拒绝之前被调用。如果承诺已经达成，就没有办法“取消”了。

#### 自动忽略上次通话

[awesome-only-resolves-last-promise](https://github.com/slorber/awesome-only-resolves-last-promise)是一个库，确保我们只处理最后一次异步调用的结果:

```
import { onlyResolvesLast } from 'awesome-only-resolves-last-promise';

const fetchStarwarsHeroLast = onlyResolvesLast(
  fetchStarwarsHero,
);

const promise1 = fetchStarwarsHeroLast(1);
const promise2 = fetchStarwarsHeroLast(2);
const promise3 = fetchStarwarsHeroLast(3);

// promise1: won't resolve
// promise2: won't resolve
// promise3: WILL resolve 
```

Enter fullscreen mode Exit fullscreen mode

### 悬念呢？

它应该可以防止这些问题，但让我们等待官方发布:)

## 结论

对于您的下一个 React 数据加载用例，我希望您考虑正确处理竞争条件。

我还可以建议在开发环境中对您的 API 请求硬编码一些小的延迟。潜在的竞争条件和糟糕的装载体验将更容易被注意到。我认为更安全的做法是强制规定这种延迟，而不是期望每个开发人员都打开 devtools 中的慢速网络选项。

我希望你对这篇文章感兴趣，并且学到了一些东西，这是我的第一篇技术博客文章:)

* * *

最初发布在[我的网站](https://sebastienlorber.com/handling-api-request-race-conditions-in-react)

如果你喜欢，用一个[转发](https://twitter.com/sebastienlorber/status/1167368979125608450)来传播这个消息

浏览器演示代码或纠正我在博客报告上发布的错别字

想了解更多类似的内容，请订阅[我的邮件列表](https://mailchi.mp/4ea4df0b54f7/sebastienlorber)并在[推特](https://twitter.com/sebastienlorber)上关注我。

感谢我的审稿人:[王自如](https://twitter.com/swyx)，[马特乌斯·布尔琴斯基](https://twitter.com/AndaristRake)，[安德烈·卡拉赞斯](https://twitter.com/Andrei_Calazans)，[阿德里安·卡罗利](https://twitter.com/icookandcode)，[克莱门特·奥里奥尔](https://twitter.com/oriolclement)，[蒂波特·杜索伊特](https://github.com/tidusia)，[伯纳德·普拉茨](https://twitter.com/guyzmo)