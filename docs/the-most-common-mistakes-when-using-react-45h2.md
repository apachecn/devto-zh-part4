# 使用 React 时最常见的错误

> 原文：<https://dev.to/clarity89/the-most-common-mistakes-when-using-react-45h2>

*这篇文章最初发表在[我的个人博客](https://claritydev.net/blog/the-most-common-mistakes-when-using-react)。*

在回答关于栈溢出的 React 相关问题时，我注意到人们对这个库有几个主要的问题。我决定写一些最常见的问题，并展示如何处理它们，希望对那些刚接触 React 的人或任何正在努力理解其基本概念的人有所帮助。使用基于类的组件和使用钩子的功能性组件的缺陷可以互换。

#### 1。直接修改状态

React 中的状态被认为是不可变的，因此不应该直接更改。应该使用一个特殊的`setState`方法和来自`useState`钩子的 setter 函数。考虑下面的例子，在这个例子中，您希望根据复选框的状态更新数组中特定对象的`checked`字段。

```
 const updateFeaturesList = (e, idx) => {
      listFeatures[idx].checked = e.target.checked;
      setListFeatures(listFeatures);
    }; 
```

Enter fullscreen mode Exit fullscreen mode

这段代码的问题是，对状态的更改不会反映在 UI 中，因为状态是用相同的对象引用更新的，因此不会触发重新呈现。不直接改变状态的另一个重要原因是，由于它的异步特性，稍后的状态更新可能会覆盖直接对状态进行的更新，从而导致一些不可避免的错误。在这种情况下，正确的方法是使用 useState 的 setter 方法。

```
 const updateFeaturesList = (e, idx) => {
      const { checked } = e.target;
      setListFeatures(features => {
        return features.map((feature, index) => {
          if (idx === index) {
            feature = { ...feature, checked };
          }
          return feature;
        });
      });
    }; 
```

Enter fullscreen mode Exit fullscreen mode

通过使用`map`和对象扩散，我们也确保我们没有改变原始状态的项目。

#### 2。在初始状态下设置错误的值类型

将初始状态值设置为`null`或空字符串，然后在 render 中访问该值的属性，就好像它是一个对象一样，这是一个很常见的错误。这同样适用于不为嵌套对象提供默认值，然后试图在 render 或其他组件方法中访问它们。

```
 class UserProfile extends Component {
      constructor(props) {
        super(props);

        this.state = {
          user: null
        };
      }

      componentDidMount() {
        fetch("/api/profile").then(data => {
          this.setState({ user: data });
        });
      }

      render() {
        return (
          <div>
            <p>User name:</p>
            <p>{this.state.user.name}</p> // Cannnot read property 'name' of null
          </div>
        );
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

类似的错误发生在将初始状态的值设置为空数组，然后试图从中访问第 n 项时。当 API 调用获取数据时，组件将呈现提供的初始状态，试图访问`null`或`undefined`元素的属性将导致错误。因此，让初始状态接近地代表更新后的状态是很重要的。在我们的例子中，正确的状态初始化如下:

```
 class UserProfile extends Component {
      constructor(props) {
        super(props);

        this.state = {
          user: {
            name: ""
            // Define other fields as well
          }
        };
      }

      componentDidMount() {
        fetch("/api/profile").then(data => {
          this.setState({ user: data });
        });
      }

      render() {
        return (
          <div>
            <p>User name:</p>
            <p>{this.state.user.name}</p> // Renders without errors
          </div>
        );
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

从 UX 的角度来看，在获取数据之前，最好显示某种加载器。

#### 3。忘记了`setState`是异步的

另一个常见的错误是试图在设置状态值后立即访问它。

```
 handleChange = count => {
      this.setState({ count });
      this.props.callback(this.state.count); // Old state value
    }; 
```

Enter fullscreen mode Exit fullscreen mode

设置新值不会立即发生，通常会在下一次可用渲染时完成，或者可以批处理以优化性能。因此，在设置状态值后访问它可能不会反映最新的更新。这个问题可以通过对`setState`使用可选的第二个参数来解决，这是一个回调函数，在状态用其最新值更新后调用。

```
 handleChange = count => {
      this.setState({ count }, () => {
        this.props.callback(this.state.count); // Updated state value
      });
    }; 
```

Enter fullscreen mode Exit fullscreen mode

但是钩子就完全不同了，因为`useState`的 setter 函数没有类似于`setState`的第二个回调参数。在这种情况下[官方推荐的方式](https://github.com/facebook/react/issues/14174#issuecomment-437406821)是使用`useEffect`挂钩。

```
 const [count, setCount] = useState(0)

    useEffect(() => {
      callback(count); // Will be called when the value of count changes
    }, [count, callback]);

    const handleChange = value => {
      setCount(value)
    }; 
```

Enter fullscreen mode Exit fullscreen mode

应该注意的是，`setState`并不是异步的，它返回一个承诺。所以在上面拍打`async/await`或者使用`then`都不会起作用(另一个常见错误)。

#### 4。错误地依赖当前状态值来计算下一个状态

这个问题与上面讨论的问题相关，因为它也与异步状态更新有关。

```
 handleChange = count => {
      this.setState({ count: this.state.count + 1 }); // Relying on current value of the state to update it
    }; 
```

Enter fullscreen mode Exit fullscreen mode

这种方法的问题在于，在设置新状态时，count 的值可能没有被正确地更新，这将导致新状态值被错误地设置。这里正确的做法是使用`setState`的函数形式。

```
 increment = () => {
      this.setState(state => ({ count: state.count + 1 })); // The latest state value is used
    }; 
```

Enter fullscreen mode Exit fullscreen mode

在应用更新时,`setState`的函数形式有第二个参数- `props`,其用法与 state 类似。

同样的逻辑也适用于`useState`钩子，setter 接受一个函数作为参数。

```
 const increment = () => {
     setCount(currentCount => currentCount + 1)
    }; 
```

Enter fullscreen mode Exit fullscreen mode

#### 5。省略`useEffect`的依赖数组

这是一个不太常见的错误，但还是会发生。尽管完全有理由省略`useEffect`的依赖数组，但是当回调修改状态时这样做可能会导致无限循环。

#### ⑥。将对象或其他非原始类型的值传递给`useEffect`的依赖数组

类似于上面的情况，但更微妙的错误，是跟踪对象，数组或效果挂钩的依赖数组中的其他非原始值。考虑下面的代码。

```
 const features = ["feature1", "feature2"];

    useEffect(() => {
      // Callback 
    }, [features]); 
```

Enter fullscreen mode Exit fullscreen mode

在这里，当我们将一个数组作为依赖项传递时，React 将只存储对它的引用，并将其与数组的前一个引用进行比较。然而，由于它是在组件内部声明的，`features`数组在每次渲染时都被重新创建，这意味着它的引用每次都是一个新的引用，因此不等于由`useEffect`跟踪的引用。最终，回调函数将在每次渲染时运行，即使数组没有改变。对于原始值(如字符串和数字)来说，这不是问题，因为在 JavaScript 中它们是通过值而不是引用进行比较的。

有几种方法可以解决这个问题。第一种选择是将变量声明移到组件之外，这样就不会在每次渲染时重新创建它。然而，在某些情况下这是不可能的，例如，如果我们正在跟踪 props 或者被跟踪的依赖项是组件状态的一部分。另一个选择是使用定制的[深度比较钩子](https://github.com/kentcdodds/use-deep-compare-effect)来正确地跟踪依赖引用。一个更简单的解决方案是将值打包到`useMemo`钩子中，这样在重新渲染时会保留引用。

```
 const features = useMemo(() => ["feature1", "feature2"], []);

    useEffect(() => {
      // Callback 
    }, [features]); 
```

Enter fullscreen mode Exit fullscreen mode

希望这个列表能帮助你避免最常见的 React 问题，并加深对主要陷阱的理解。

对这篇文章有任何问题/评论或其他类型的反馈吗？请在评论中或在 [Twitter](https://mobile.twitter.com/Clarity_89) 上告诉我。