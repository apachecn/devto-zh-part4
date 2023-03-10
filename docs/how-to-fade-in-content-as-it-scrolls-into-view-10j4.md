# 当内容滚动到视图中时如何淡入

> 原文：<https://dev.to/selbekk/how-to-fade-in-content-as-it-scrolls-into-view-10j4>

今天，我想向你展示一种以漂亮的方式显示内容的技巧——当内容出现时淡入显示！

## 🎚的时尚 slidy part

让我们从指定所需的 CSS 开始。我们创建了两个类——一个`fade-in-section`基类和一个`is-visible`修饰符类。当然，你可以随心所欲地给它们命名。

`fade-in-section`类应该隐藏我们的组件，而`is-visible`类应该显示它。我们将使用 CSS 转换在它们之间进行转换。

代码是这样的:

```
.fade-in-section {
  opacity: 0;
  transform: translateY(20vh);
  visibility: hidden;
  transition: opacity 0.6s ease-out, transform 1.2s ease-out;
  will-change: opacity, visibility;
}
.fade-in-section.is-visible {
  opacity: 1;
  transform: none;
  visibility: visible;
} 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们使用`transform`属性最初将我们的容器向下移动 1/5 的视口(或者 20 个视口高度单位)。我们还指定初始不透明度为 0。

通过转换这两个属性，我们将得到我们想要的效果。我们还将`visibility`属性从`hidden`转换到`visible`。

下面是实际效果:

[https://codesandbox.io/embed/festive-gould-exvw7](https://codesandbox.io/embed/festive-gould-exvw7)

看起来很酷吧？现在，如果每当我们将一个新的内容块滚动到视口中时都有这种效果，那该有多酷？

## 浮夸的部分👋

如果在你的内容可见的时候触发一个事件不是很好吗？我们将使用`IntersectionObserver` DOM API 来实现该行为。

API 是一个非常强大的工具，可以跟踪屏幕上的内容，无论是部分还是全部。如果你想深入了解，我建议你阅读这篇关于这个主题的 [MDN 文章](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API)。

但是，快速总结一下，一个交叉点观察器接受一个 DOM 节点，并在它进入(或退出)视口时调用一个回调函数。它为我们提供了一些位置数据，以及类似于`isIntersecting`的属性，这些属性告诉我们某些东西是否可见。

不过，在本文中，我们并没有深入挖掘您可以使用交叉点观察器做的其他很酷的事情，我们只是实现了一个很好的“入口淡入”功能。由于我们使用 React，我们可以编写一个很好的可重用组件，可以在我们的应用程序中重用。

下面是实现我们组件的代码:

```
function FadeInSection(props) {
  const [isVisible, setVisible] = React.useState(true);
  const domRef = React.useRef();
  React.useEffect(() => {
    const observer = new IntersectionObserver(entries => {
      entries.forEach(entry => setVisible(entry.isIntersecting));
    });
    observer.observe(domRef.current);
    return () => observer.unobserve(domRef.current);
  }, []);
  return (
    <div
      className={`fade-in-section ${isVisible ? 'is-visible' : ''}`}
      ref={domRef}
    >
      {props.children}
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个实现它的沙箱:

[https://codesandbox.io/embed/beautiful-wiles-k23w5](https://codesandbox.io/embed/beautiful-wiles-k23w5)

如果你正在寻找一个复制粘贴的解决方案，这就是你要找的。

### 正在发生的事情——一步一步来

如果你想了解发生了什么，我写了一个分步指南，解释发生了什么。

首先，我们调用三个内置的 React 钩子- `useState`、`useRef`和`useEffect`。你可以在[文档](https://reactjs.org/docs/hooks-reference.html#basic-hooks)中读到更多关于这些钩子的信息，但是在我们的代码中我们做了以下事情:

1.  用`useState`创建一个状态变量，指示该部分是否可见。我们将其默认为`false`
2.  用`useRef`创建对 DOM 节点的引用
3.  创建交叉点观察器，并用`useEffect`开始观察

交叉点观察器的设置可能看起来有点陌生，但是一旦你理解了发生了什么，它就非常简单了。

首先，我们创建 IntersectionObserver 类的一个新实例。我们传入一个回调函数，每当注册到这个观察者的任何 DOM 元素改变它的“状态”(即，每当你滚动、缩放或新的东西出现在屏幕上)时，这个函数都会被调用。然后，我们告诉观察者实例用`observer.observe(domRef.current)`观察我们的 DOM 节点。

然而，在我们完成之前，我们需要清理一下——无论何时卸载 DOM 节点，我们都需要从 DOM 节点中删除交叉点监听器！幸运的是，我们可以从`useEffect`返回一个清理函数，它将为我们做这件事。

这就是我们在`useEffect`实现的最后所做的——我们返回一个调用我们的观察者的`unobserve`方法的函数。(感谢[金成](https://dev.to/dance2die)在评论区给我指出了这一点！)

我们传递给观察者的回调是用一个条目对象列表调用的——每次调用`observer.observe`方法都有一个条目对象。因为我们只调用它一次，我们可以假设列表只包含一个元素。

我们通过调用它的设置器——`setVisible`函数——用`entry.isIntersecting`的值来更新`isVisible`状态变量。我们可以进一步优化它，只调用一次——这样就不会重新隐藏我们已经看过的内容。

我们通过将我们的 DOM ref 附加到实际的 DOM 来结束我们的代码——通过将它作为`ref`属性传递给我们的`<div />`。

然后我们可以像这样使用我们的新组件:

```
<FadeInSection>
  <h1>This will fade in</h1>
</FadeInSection>

<FadeInSection>
  <p>This will fade in too!</p>
</FadeInSection>

<FadeInSection>
  <img src="yoda.png" alt="fade in, this will" />
</FadeInSection> 
```

Enter fullscreen mode Exit fullscreen mode

这就是当你滚动到视图中时，如何让内容淡入的方法！

我很想在评论中看到你如何用不同的方法达到同样的效果——或者有没有什么方法可以优化我写的代码。

感谢阅读！

## 关于可达性的最后一点说明

虽然动画看起来很酷，但有些人在身体上有问题。在这种情况下，动画不利于用户体验。幸运的是，您可以为这些用户实现一个特殊的媒体查询——即`prefers-reduced-motion`。你可以(也应该！)阅读关于这个主题的文章。