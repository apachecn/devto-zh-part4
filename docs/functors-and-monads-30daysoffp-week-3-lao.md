# 函子和单子-第 3 周第 30 天

> 原文：<https://dev.to/dslemay/functors-and-monads-30daysoffp-week-3-lao>

函子和单子听起来可能像是函数式编程深处的某种黑暗的秘密成分。出于我们的目的，我们将把它们作为遵循一组基本规则的数据类型来对待。在这篇文章中，我们将深入研究函子和单子的工作定义，以及将它们联系在一起的工作示例。

## 什么是函子

函子是一种包含一个`map`函数并遵循若干规则的类型。在它们的核心，它们是封装一个值的容器。map 函数进入容器，对内部值执行操作，并最终将其返回给容器。JavaScript 中的数组实际上是函子。惊喜！

```
const double = x => x * 2

const arr = [7, 3, 12, 5]
const dblArr = arr.map(double) // [ 14, 6, 24, 10 ] 
```

数组类型有一个`map`方法，它接受一个函数来对其中的值执行操作。操作完成后，新值作为新数组返回到容器中。这使它成为一个函子。我们也可以创建自己的函子，记住这两条规则。这里我们将创建一个保存值的`Container`函子。这段代码改编自弗里斯比博士的[函数式编程基本指南](https://mostly-adequate.gitbooks.io/mostly-adequate-guide/)。如果你有兴趣学习更多关于函数式编程的知识，包括函子和单子，我强烈推荐你阅读这本免费的书。

```
const add = x => y => x + y

class Container {
  static of(x) {
    return new Container(x)
  }

  constructor(x) {
    this.$value = x
  }

  map(fn) {
    return Container.of(fn(this.$value))  
  }
} 

console.log(Container.of(2).map(add(2)))
// Container { '$value': 4 } 
```

这里我们有一个在闭包里保存值的基本函子。每次运行`map`时，我们都会得到一个带有当前值的容器的新实例。这个函子是基本的，但是作为更复杂的函子的构建块。

## 什么是单子

单子是遵循一些附加规则的函子。所有单子都必须包含一个`of`方法，类似于上面的容器例子。此方法用一个值实例化容器，以便可以立即映射它。实现`of`方法的函子被称为*指向函子*。

在某些情况下，函子可以互相嵌套。这需要额外调用`map`在到达内部值之前展开这些层。这是通过一个`join`的方法来减轻展平额外的层。一个`Maybe`类型的基本实现可以帮助说明这一点。

```
class Maybe {
  static of(x) {
    return new Maybe(x)
  }

  get isNothing() {
    return this.$value === null || this.$value === undefined
  }

  constructor(x) {
    this.$value = x
  }

  join() {
    return this.isNothing ? Maybe.of(null) : this.$value
  }

  map(fn) {
    return this.isNothing ? this : Maybe.of(fn(this.$value))
  }

  inspect() {
    return this.isNothing ? 'Nothing' : `Just(${this.$value})`
  }
}

const nestedMaybe = Maybe.of(Maybe.of(3))
// Maybe(Maybe(3))

nestedMaybe.join()
// Maybe(3) 
```

Maybe 类型是一个单子，它跟踪容器的两个潜在状态。容器可以`Just`有一个值，也可以是`Nothing`。在 JavaScript 中，`Nothing`不同于`null`或`undefined`的用法。在这些情况下，我们需要防止执行无效的操作，比如从`undefined`访问属性。即将到来的[可选链接](https://github.com/tc39/proposal-optional-chaining)和[空合并](https://github.com/tc39/proposal-nullish-coalescing)功能目前处于第 3 阶段，希望能够缓解这种情况。可能类型的行为不同。如果它的当前值是`Nothing`，任何后续的`map`调用都会被绕过。只要 Maybe 类型停留在`Just`案例中，它就会继续执行`map`调用。当我们准备好从 Maybe 类型中提取值时，我们还可以为`Nothing`分支提供一个回退值。

当数据的结构在运行时未知时，类型可能是有用的。REST 调用就是一个例子。由于 REST 中没有数据契约，后端结构的变化会导致以前依赖的键不存在。我们希望他们在成功的案例中出现，但他们可能不会。

## 把所有的东西放在一起

既然我们知道什么是单子和函子，为什么它实际上很重要？最近，我一直在用一个函数包装器来测试 currying 调用。这个代码示例使用了 [crocks 库](https://crocks.dev/docs/getting-started.html)，它提供了许多功能实用程序，包括上面讨论的 may 类型。你可以在代码沙盒上找到下面代码的[运行示例。](https://codesandbox.io/s/monad-experimentation-iopyr) 

```
import Async from 'crocks/Async'
import chain from 'crocks/pointfree/chain'
import compose from 'crocks/helpers/compose'
import curry from 'crocks/helpers/curry'
import getProp from 'crocks/Maybe/getProp'
import map from 'crocks/pointfree/map'
import maybeToArray from 'crocks/Maybe/maybeToArray'

const safeFetch = curry((baseUrl, endpoint, options) =>
  Async((reject, resolve) => {
    fetch(`${baseUrl}/${endpoint}`, options)
      .then(res => res.json())
      .then(resolve)
      .catch(reject)
  })
)

const printRepos = repos => {
  const list = document.getElementById('repo-list');
  repos.forEach(repo => {
    const li = document.createElement('li');
    li.innerHTML = repo;
    list.append(li);
  })
}

const fetchGithub = safeFetch('https://api.github.com');
const fetchDslemay = fetchGithub('users/dslemay/repos')({});

fetchDslemay.fork(
  console.log,
  compose(
    printRepos,
    chain(maybeToArray),
    map(getProp('name'))
  )
) 
```

这个例子使用了两种不同的数据类型:Async 和 Maybe。根据上面讨论的规则，Maybe 类型是一个单子。Async 的行为类似于 promise，因为它接收已解析和已拒绝的参数。然而，它们是相反的，首先是被拒绝的。使用异步类型，我们将这个函数的执行延迟到以后。要运行它，我们调用`fork`方法并提供两个函数，一个在被拒绝的情况下运行，另一个在成功的情况下运行。

在我们的成功案例中，我们构建了一系列函数来运行数据结果。我们首先运行`getProp`来提取数据数组中每个对象的`name`属性。该函数返回一个可能类型。如果找到该属性，则将其放在`Just`分支中，否则 Maybe 的值为`Nothing`。`chain`函数处理对结果的调用`map`并立即调用`join`。这对于嵌套类型的操作很有用，有助于我们避免分别调用这两个方法。crocks 的`maybeToArray`实用程序提供了一种将 may 类型的数组转换为常规数组的方法。如果数组中的项是一个`Just`类型，它将被它的内部值替换。如果数组中的项目是`Nothing`类型，它将不会被添加到结果数组中。给定一个`Nothing`类型的数组，这个函数将返回一个空数组。最后，我们将存储库名称数组传递给`printRepos`函数，并在 DOM 中显示它们。

## TLDR

*   仿函数是一种数据类型，它包含一个`map`方法并遵循几个规则
*   包含一个`of`方法来立即实例化它的函子是一个*指向函子*。
*   除了函子，单子还有额外的规则。它们必须是指向函子，但也利用一个`join`方法来展平嵌套类型

在使用 JavaScript 时，函子和单子可以提供额外的工具和类型。归根结底，它们是服务于特定目的并遵循一系列规则的容器。它们可能包含许多其他方法，但这些核心方法是对它们进行分类的必要基础。