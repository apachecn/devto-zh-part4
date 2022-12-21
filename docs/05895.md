# 使用 React 挂钩计算组件安装上的值:状态与参考

> 原文：<https://dev.to/raicuparta/compute-values-on-component-mount-with-react-hooks-state-vs-ref-4epk>

我最近碰到这个问题:

> 我有一个功能性的 React 组件，它在组件挂载时计算一个值。安装后，该值不会更新。哪种方法更好:
> 
> `const value = useMemo(() => computeValue(), [])`
> 还是
> `const [value] = useState(() => computeValue())`
> ？

答案是它们都可行，但都不理想。我们来看看为什么。

* * *

## `useMemo`

```
import computeValue from 'expensive/compute';

// ...

const value= useMemo(computeValue, []); 
```

Enter fullscreen mode Exit fullscreen mode

乍看之下，`useMemo`似乎非常适合这一点。只有当依赖项列表(第二个参数)发生变化时，它才会重新计算值。使用空数组作为依赖项列表，将只在第一次呈现时计算该值。这很有效。但问题是:

> [**你可以依赖`useMemo`作为性能优化，而不是作为语义保证**。](https://reactjs.org/docs/hooks-reference.html#usememo)将来，React 可能会选择“忘记”一些先前记忆的值，并在下次渲染时重新计算它们，例如为屏幕外组件释放内存。编写您的代码，使其在没有`useMemo`的情况下仍然可以工作，然后添加它以优化性能。

所以我们不能依赖`useMemo`来确保一个值只被计算一次。即使它现在工作得很好，我们也不应该假设将来行为会是一样的。

那么我们能依靠什么*？*

* * *

## `useState`

```
import computeValue from 'expensive/compute';

// ...

const [value] = useState(computeValue) 
```

Enter fullscreen mode Exit fullscreen mode

这个更接近正确答案，而且它实际上还挺管用的。但是它*语义不正确*。

当我们将`computeValue`函数作为参数传递给`useState`时，它被用于[惰性初始化](https://reactjs.org/docs/hooks-reference.html#lazy-initial-state)。结果是将计算该值，但仅在第一次渲染时。好像是我们要找的。

问题是**这将阻止第一次渲染**，直到我们的`computeValue`函数完成。该值将不再更新。那么这真的是组件状态的好用途吗？让我们想想，React 组件中状态的目的是什么？

当我们希望组件能够自我更新时，我们需要状态。这里是这样吗？在组件的生命周期中只有一个可能的值，所以没有。我们使用状态是为了它的目的之外的东西。

如果不在州内，那么我们把计算出来的值存储在哪里呢？

* * *

## `useRef`

在使用钩子之前，您可能认为 refs 是用来访问子组件和聚焦输入的东西。[但是`useRef`比](https://reactjs.org/docs/hooks-reference.html#useref)简单多了:

> 本质上，useRef 就像一个“盒子”,可以在它的。当前属性。
> 
> `useRef()`比`ref`属性更有用。它便于保存任何可变值，就像在类中使用实例字段一样。
> 
> 变异了。当前属性不会导致重新呈现。

这在这里有什么用？

```
import computeValue from 'expensive/compute';

// ...

const value = useRef(null)

if (value.current === null) {
  value.current = computeValue()
} 
```

Enter fullscreen mode Exit fullscreen mode

我们用`null`初始化我们的 ref。然后，我们立即将`value.current`更新为计算出的值，但前提是它还没有被定义。

由此产生的行为与前面使用`useState`的示例相同。**计算数值时，渲染受阻**。之后，该值立即可用于第一次渲染。

区别只是在实现上:我们没有向组件添加不必要的状态。相反，我们使用 ref 来实现其最初的目的:保持一个在渲染之间持续的值。

但是如果我们*不希望*在计算值的时候阻止渲染呢？

* * *

## `useState`和`useEffect`

这个解决方案对任何尝试过 React 钩子的人来说都很熟悉，因为它是在组件挂载上做任何事情的标准方式:

```
import computeValue from 'expensive/compute';

// ...

const [value, setValue] = useState(null)

useEffect(() => {
  setValue(computeValue)
}, []) 
```

Enter fullscreen mode Exit fullscreen mode

每当依赖列表改变时，`useEffect`将在第一次渲染后运行*。在这种情况下，我们将依赖列表设置为`[]`，一个空数组。这将使效果只在第一次渲染后运行，而不再运行。它不*不*阻止任何渲染。*

当计算值时，我们的第一个渲染将使用`value = null`运行。一旦计算完成，将调用`setValue`，并使用新值触发重新渲染。除非重新安装组件，否则效果不会再次运行。

这一次拥有状态是有意义的，因为组件可以有两种状态:计算值之前和之后。这还有一个好处:我们可以显示一个“正在加载…”值正在计算时的消息。

* * *

## 结论:状态 vs 参照 vs 备忘录

这里的主要教训是这些之间的区别:

*   `useState`:
    *   用于存储跨呈现保持的值；
    *   更新触发重新渲染；
    *   通过 setter 函数更新。
*   `useRef`:
    *   也用于存储跨渲染保持的值；
    *   更新不会触发重新渲染；
    *   可通过`.current`属性直接改变。
*   `useMemo`:
    *   仅用于性能优化

        * * *