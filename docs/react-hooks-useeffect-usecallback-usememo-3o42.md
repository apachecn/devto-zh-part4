# 反应钩子:使用效果，使用回调，使用备忘录

> 原文：<https://dev.to/devcord/react-hooks-useeffect-usecallback-usememo-3o42>

React ships 有一大堆钩子，当你一次学习它们的时候，可能有点难以掌握。这篇文章将帮助你理解其中三种的区别和使用案例。

# 使用效果

一个纯组件只与它自己和它的子组件交互。任何时候你需要与组件外的世界互动，你都在处理*副作用*。

React 为我们处理这些问题提供了一个方便的钩子。`React.useEffect`钩子让我们指定一个处理外力的函数，提供第二个函数来清理它，并删除一个依赖关系列表，这样当一个依赖关系改变时，我们可以重新运行效果。

# 使用效果的例子

### 更新页面标题

该效果将在组件第一次呈现时运行，并且只有在标题更改时才会再次运行。

```
const [title, setTitle] = React.useState("Hooks 101");

React.useEffect(() => {
    document.title = title;
}, [title]); 
```

Enter fullscreen mode Exit fullscreen mode

### 从一个 API 取数据到本地状态。

因为我们的状态改变不会影响返回的产品列表，所以我们可以传递一个空数组`[]`作为我们的依赖项，这样只有当组件第一次被挂载时效果才会运行。

```
const [products, setProducts] = React.useState([]);

React.useEffect(() => {
    getProducts()
    .then(products => {
        setProducts(products);
    })
}, []); 
```

Enter fullscreen mode Exit fullscreen mode

### 根据查询从 API 获取数据到本地状态。

如果我们有一个查询或过滤器来修改我们想要的 API 数据集，那么我们可以将它作为一个依赖项来传递，以确保 React 在每次组件使用新的查询进行渲染时都运行这个效果。

```
const [products, setProducts] = React.useState([]);
const [query, setQuery] = React.useState("");

React.useEffect(() => {
    getProducts({name: query})
    .then(products => {
        setProducts(products);
    })
}, [query]); 
```

Enter fullscreen mode Exit fullscreen mode

### 调度一个 Redux 动作。

如果您的 GET 操作已经简化为 Redux 状态，那么您不需要在本地维护它。

通过将`products.length`作为依赖项传递，您只需运行这个

```
const dispatch = Redux.useDispatch();
const products = Redux.useSelector(state => state.products);

React.useEffect(() => {
    dispatch(GetProducts())
}, []); 
```

Enter fullscreen mode Exit fullscreen mode

# 使用备忘录

与 useEffect 不同，`React.useMemo`不会在您每次更改它的一个依赖项时触发。

记忆化的函数将首先检查自上次渲染以来依赖关系是否发生了变化。如果是，它执行函数并返回结果。如果为 false，它只返回上次执行的缓存结果。

这对于昂贵的操作来说是很好的，比如转换 API 数据或者做一些你不想重复做的计算

### 使用备忘录示例

```
const posts = Redux.useSelector(state => state.posts);

const tags = React.useMemo(() => {
    return getTagsFromPosts(posts)
}, [posts]); 
```

Enter fullscreen mode Exit fullscreen mode

# 使用回调

这是记忆功能的一个特例。因为 javascript 通过引用来比较相等性，所以组件第一次呈现时创建的函数将不同于随后呈现时创建的函数。

如果你试图将一个函数作为道具或状态来传递，这意味着它每次都会被当作一个道具变化来处理。通过将它包装在 useCallback 中，React 将知道它是同一个函数。如果依赖关系发生变化，您仍然可以添加一个依赖关系数组来触发重新计算。

这是一个避免子组件重新呈现的强大用例

### 使用回调的例子

这个组件每渲染一次，也会触发按钮组件的整个重新渲染，因为`removeFromCart`函数每次都是唯一的。

```
const dispatch = useDispatch();

const removeFromCart = () => dispatch(removeItem(product.id));

return (
    <Button onClick={removeFromCart}>Delete</Button> ); 
```

Enter fullscreen mode Exit fullscreen mode

用这个替换我们的回调将完全避免这个问题。现在，只有当我们的产品 ID 改变时，该按钮才会重新呈现，因此它将从我们的购物车中删除新产品。

```
const removeFromCart = React.useCallback(() => {
    dispatch(removeItem(product.id))
}, [product.id]); 
```

Enter fullscreen mode Exit fullscreen mode

# 进一步阅读

[https://overreacted.io/a-complete-guide-to-useeffect/](https://overreacted.io/a-complete-guide-to-useeffect/)

[https://medium . com/@ vcarl/everything-you-need-know-on-react-hooks-8f 680 DFD 4349](https://medium.com/@vcarl/everything-you-need-to-know-about-react-hooks-8f680dfd4349)

[https://kentcdodds.com/blog/usememo-and-usecallback](https://kentcdodds.com/blog/usememo-and-usecallback)

[https://www.robinwieruch.de/react-hooks-fetch-data/](https://www.robinwieruch.de/react-hooks-fetch-data/)

[https://stack overflow . com/questions/54371244/what-the-intent-of-use-reactions-use callback-hook-in-place-of-use effect](https://stackoverflow.com/questions/54371244/what-is-the-intention-of-using-reacts-usecallback-hook-in-place-of-useeffect)

[https://stack overflow . com/questions/54963248/what ' s-difference-of-use callback-and-use memo-in-practice/54965033 # 54965033](https://stackoverflow.com/questions/54963248/whats-the-difference-between-usecallback-and-usememo-in-practice/54965033#54965033)