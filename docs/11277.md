# lodash memoire 你不应该用 lodash 来记忆

> 原文：<https://dev.to/nioufe/you-should-not-use-lodash-for-memoization-3441>

几天前，我发现了一个导致 react 组件无法更新的错误。调试显示原因是 lodash 的`memoize`函数。

<figure>`const lodash = require('lodash'); const add = function(a, b){return a + b}; const memoizedAdd = lodash.memoize(add); console.log('1 + 1 = 1 + 2', memoizedAdd(1,2) === memoizedAdd(1,1));`

<figcaption>This is not really what I expected</figcaption>

</figure>

在前端项目中，我们使用记忆化进行不同的优化:

*   避免组件渲染- `React.memo`
*   避免重新计算内部组件状态- `useMemo`
*   避免重新计算来自 redux 状态的信息- `createSelector`来自 reselect

目标总是相同的:如果输入与前一个调用相同，不要重复昂贵的计算。直接返回最后的计算结果会更快。[维基百科上关于记忆的更多信息](https://en.wikipedia.org/wiki/Memoization)

## 在 react 应用程序中使用 memoize

`useMemo`、`React.memo`和`createSelector`通常足以满足你所有的记忆需求。然而钩子在*类组件*中不起作用。如果您的代码库中还有一些，您需要一个定制的记忆功能来复制`useMemo`的功能。在 [react 文档](https://reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html#what-about-memoization)中描述了一个实现。

<figure>

```
// function component with memoization

const ComponentWithMemo = ({propA, propB}) => {

    const memoizedValue = useMemo(
        () => computeExpensiveValue(propA,propB), 
        [propA, propB]
    );

    return <p>{memoizedValue}</p> }

//class component with memoization

import memoize from 'memoize-one';

class ComponentWithMemo extends React.Component {
   // Need to define a memoized function in the component
   memoizedCompute = memoize(computeExpensiveValue)

   render() {
       const {propA, propB} = this.props;
       // and call it on render
       const memoizedValue = this.memoizedCompute(propA, propB);
       return <p>{memoizedValue}</p>
   }
} 
```

<figcaption>2 implementations of memoize to avoid re-computing on render</figcaption>

</figure>

Lodash 非常常见，使用`lodash/memoize`似乎是一个很好的选择，可以在不添加(又一个)依赖项的情况下实现该模式。

## 问题 1: Lodash 只使用第一个参数

下面是 lodash 内部对第一个例子的解释:

<figure>

```
var memoizedAdd = _.memoize(add); // cache = {}
memoizedAdd(1,1) // cache[1] = 2; return 2;
memoizedAdd(1,2) // return cache[1]; <== My :bug: is here
memoizedAdd(2,1) // cache[2] = 3; return 3; 
```

<figcaption>Step-by-step of the memoized add example</figcaption>

</figure>

这是因为 lodash 的 memoize 函数默认情况下只使用第一个参数作为缓存键。因此，只要传递相同的第一个参数，函数总是返回相同的结果。

另一方面，`memoize-one`和运行在`react`或`reselect`中的其他实现在任何参数改变时重新计算函数，因此它总是返回正确的结果。

该问题不是由未记录的 lodash 行为引起的。事实上，文档清楚地说明了他们使用第一个参数作为缓存键。这些错误的根本原因是它**与其他实现**非常不同，这些实现通常存在于同一个项目中，并且应该提供相同的功能。

## 问题二:不需要无限缓存

虽然第一个差异可能会导致可见的错误，但这一个可能会影响性能。这通常很难察觉，但它会对用户体验产生很大影响。

`// const lodash = require('lodash'); const add = function(a, b){return a + b}; const lodashAdd = lodash.memoize(add);` `// use the memoized add 1000 times for(let i = 0; i<1000; i++){ lodashAdd(i,2); } console.log('lodash cache size: ', lodashAdd.cache.size);`

运行记忆化函数 1000 次会在缓存中保存 1000 个结果。这是否意味着 memoize 是一个好的缓存？算是吧。但这不是我们需要的记忆功能。

Lodash 使用一个`Map`来缓存与一个键相关的所有函数结果。

```
// from https://github.com/lodash/lodash/blob/master/memoize.js
memoized.cache = cache.set(key, result) || cache
...
memoize.Cache = Map 
```

这意味着所有的键和返回值将被永久保存(默认)*。*

 *如果你没有很多不同的钥匙，你就看不出区别。如果您使用唯一的 id，这可能会有问题。内存泄漏很难跟踪，因为它们可能只发生在特定的用例中，比如长时间保持打开的页面。因此，不建议使用默认情况下会造成泄漏的缓存。

您可以配置 lodash 缓存来限制保存值的数量。我认为在前端应用程序中，memoize 缓存的最佳限制是一个值:最新计算的值。

记忆化是用来避免重新计算昂贵的东西，使渲染速度更快。但是瓶颈不仅仅是重新计算一件事。当应用程序在每次更改时都要重新计算每个开销很大的操作时，就会出现性能问题。

使用缓存仅包含最后一个值的内存化允许您的应用程序只进行受更改影响的少量昂贵的计算。这在大多数情况下应该足够了。

注意:如果你有非常昂贵的操作，甚至一次都做不完，那么记忆化就不是解决这个问题的合适工具。

## 死后:生命不再存在

修复这个错误的第一个选择是配置 lodash memoize 来匹配`react`、`reselect`、`memoize-one`...实现。

```
let cacheKey;
let cacheResult;

// replace the cache to save one value
_.memoize.Cache = {
    set: (key, result) => {
        cacheKey = key;
        cacheResult = result;
    } 
    get: (key) => {
        if(cacheKey == key) {
            return cacheResult;
        }
    }
    // ... other map functions
};

// create a resolver that maps all parameters to a key
const keyResolver = (...args) => JSON.stringify(args);

const add = (a, b) => a + b;

// use the resolver in a memoized function
const memoizedAdd = _.memoize(add, keyResolver); 
```

虽然替换缓存可以一劳永逸地完成，但是使用所有参数作为缓存键的`keyResolver`需要添加到每个新的内存化函数中。

这让我选择了第二个选项:用另一个更简单的实现代替 memoize 函数。从一种内存切换到另一种内存的简单之处在于，在大多数项目中已经有很多可用的实现。

我使用来自[的`defaultMemoize`重新选择](https://github.com/reduxjs/reselect#defaultmemoizefunc-equalitycheck--defaultequalitycheck)作为短期替代，然后要么引入`memoize-one`要么转换组件以能够使用钩子。我想做的另一个改变是添加一个林挺规则，在用户导入`lodash/memoize`时警告用户。

作为整个社区的长期解决方案，我们可能希望将 lodash 函数重命名为类似于`cacheResults(fn, generateKey)`的名称，以便该名称更好地匹配默认行为，并且不会与常见的 memoize 实现冲突。*