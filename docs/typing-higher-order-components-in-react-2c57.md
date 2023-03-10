# 在 React 中键入高阶组件

> 原文：<https://dev.to/miloszpp/typing-higher-order-components-in-react-2c57>

前一段时间[我写了关于在 TypeScript 版本 3.4 中添加的](https://codewithstyle.info/TypeScript-3-4-hidden-gem-propagated-generic-type-arguments/)泛型类型参数传播特性。我解释了这种改进如何使得在 TypeScript 中进行单点风格的编程成为可能。

事实证明，在更多的情况下，传播泛型类型参数是可取的。其中之一是将通用组件传递给 React 中的更高级组件。

> ![Frederic Barthelemy profile image](img/b7d5d29e44b6c92ef8a26a3f8bcb9b24.png)弗雷德里克[@ fbartho](https://dev.to/fbartho)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)。你知道如何在高阶组件间传播泛型吗？
> 
> 我有一个泛型组件 A，它的 props 类型为 Props = pub Props<B>&injected Props
> 我有一个` bind()`-HoC，它注入了 InjectedProps
> 
> 我希望从 bind 中返回值:
> APrime < B >接受 pub Props<B>00:01AM-2019 年 7 月 26 日

*这篇文章的灵感来自于弗雷德里克·巴泰勒米在推特上发布的问题，他让我来看看。*

## 高阶组件

我就不详细解释了，因为网上已经有很多了。**高阶组件(Higher Order Component，HOC)** 是 React 框架的一个概念，它允许您抽象横切功能，并将其提供给多个组件。

从技术上讲，HOC 是一个接受一个组件并返回另一个组件的函数。它通常用一些行为来扩充源组件，或者提供源组件所需的一些属性。

下面是一个打字稿中的特设的例子:

```
const withLoadingIndicator = 
    <P extends {}>(Component: ComponentType<P>): ComponentType<P & { isLoading: boolean }> => 
        ({ isLoading, ...props }) =>
            isLoading 
                ? <span>Loading...</span>                 : <Component {...props as P} />; 
```

Enter fullscreen mode Exit fullscreen mode

正如您可以从类型签名中推断的那样，`withLoadingIndicator`是一个函数，它接受具有`P`形状属性的组件，并返回另外具有`isLoading`属性的组件。增加了基于`isLoading`属性显示加载指示器的行为。

[![HOC type](img/dbdff7b8cb53e5633ee0b73e5746768c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A9cCFw-Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tiskeso5c9v7pyvaa0tj.png)

## 问题:将通用组件传递给特设组件

到目前为止一切顺利。然而，让我们假设我们有一个**通用组件** `Header` :

```
class Header<TContent> extends React.Component<HeaderProps<TContent>> { } 
```

Enter fullscreen mode Exit fullscreen mode

...其中`HeaderProps`是一个通用类型，表示给定关联内容类型的`Header`的道具(`TContent` ):

```
type HeaderProps<TContent> = {
    content: TContent;
    title: string;
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们将`withLoadingIndicator`与这个`Header`组件一起使用。

```
const HeaderWithLoader = withLoadingIndicator(Header); 
```

Enter fullscreen mode Exit fullscreen mode

问题是，`HeaderWithLoader`的推断类型是什么？不幸的是，在 TypeScript 3.4 和更高版本中是`React.ComponentType<HeaderProps<unknown> & { isLoading: boolean; }>`，在以前的版本中是`React.ComponentType<HeaderProps<{}> & { isLoading: boolean; }>`。

如您所见，`HeaderWithLoader`是**而不是**通用组件。换句话说，`Header`的泛型类型实参是**没有传播**。等待...TypeScript 3.4 不是引入了泛型类型参数传播吗？

## 解决方法:使用函数组件！

事实上，确实如此。然而，它只对**功能**有效。`Header`是泛型类，不是泛型函数。因此，在 TypeScript 3.4 中引入的改进不适用于☹️

幸运的是，我们有**功能组件**在 React。如果我们限制`withLoadingIndicator`只使用函数组件，我们可以让类型参数传播工作。

不幸的是，我们不能使用`FunctionComponent`类型，因为它被定义为接口，而不是函数类型。然而，函数组件只不过是一个接受道具并返回`React.ReactElement`的通用函数。让我们定义自己的表示函数组件的类型。

```
type SimpleFunctionComponent<P> = (props: P) => React.ReactElement;

declare const withLoadingIndicator: 
    <P>(Component: SimpleFunctionComponent<P>) => 
        (SimpleFunctionComponent<P & { isLoading: boolean }>); 
```

Enter fullscreen mode Exit fullscreen mode

*通过使用`SimpleFunctionComponent`而不是`FunctionComponent`，我们失去了对诸如`defaultProps`、`propTypes`等属性的访问。，反正我们也不需要。*

显然，我们需要将`Header`改为函数组件，而不是类组件:

```
declare const Header: <TContent>(props: HeaderProps<TContent>) => React.ReactElement; 
```

Enter fullscreen mode Exit fullscreen mode

*我们无论如何都不能在这里使用`FunctionComponent`，因为`Header`是一个通用组件*。

现在我们来看看`HeaderWithLoader`的推断类型。它是...

```
<TContent>(props: HeaderProps<TContent> & { isLoading: boolean }) => React.ReactElement 
```

Enter fullscreen mode Exit fullscreen mode

...这看起来非常像一个通用的函数组件！

事实上，我们可以在 JSX 使用`Header`作为一个常规组件:

```
class Foo extends React.Component {
    render() {
        return (
            <HeaderWithLoader 
                title="Hello" 
                content={12345} 
                isLoading={false} />
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

最重要的是，`HeaderWithLoader`打对了！

## 总结

正如你所看到的，在 React 中输入 HOCs 会变得很棘手。建议的解决方案实际上是一种变通方法——理想情况下，TypeScript 应该能够为所有泛型类型(不仅仅是函数)传播泛型类型参数。

无论如何，这个例子说明了掌握新的 TypeScript 版本中引入的特性是多么重要。在 3.4 版本之前，甚至不可能正确地输入这个 HOC。

## 想了解更多？

你喜欢这篇打字稿吗？我打赌你也会喜欢我的书！

[高级类型脚本](https://typescriptmasterclass.com)