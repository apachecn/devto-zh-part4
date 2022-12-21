# 从头开始构建页导航组件*

> 原文：<https://dev.to/ajsmth/building-a-pager-component-from-scratch-4nlh>

在本教程中，我们将从头开始构建一个页面组件(不完全准确，我们将使用一些依赖项来帮助处理手势和动画)。第一篇文章将在介绍动画和手势等内容之前，先捕捉页面组件的核心行为。

我最初是在 react-native 中实现的，所以可以在这里找到最终产品的示例:[https://github . com/ajs mth/react-native-pager-component # readme](https://github.com/ajsmth/react-native-pager-component#readme)

**注意:**我知道这种东西的组件已经存在——这是为我自己的学习而做的，希望你也是。

在进入代码之前，让我们考虑一下我们想要做什么...

我们需要一个组件，当给定某个索引时，它将匹配的子索引移动到焦点上。因此，我们将从索引的角度来考虑——当`activeIndex`属性发生变化时，我们希望将焦点转移到与该索引匹配的子元素上:

`onChange(activeIndex) -> updateTranslation(activeIndex)`

我们可以通过在一个容器视图中水平布局我们所有的孩子来实现，就像这样:

```
const absoluteFill = {
  position: "absolute",
  left: 0,
  right: 0,
  bottom: 0,
  top: 0
};

function Pager({ children, activeIndex, size }) {
  return (
    <div
      style={{
        display: "flex",
        alignSelf: "center",

        position: "relative",
        width: size,
        height: size,
        border: "2px solid green"
      }}
    >
      <div
        style={{
          ...absoluteFill
          // we will translate this container view
        }}
      >
        {React.Children.map(children, (element, index) => (
          <PageView index={index} width={size}>
            {element}
          </PageView>
        ))}
      </div>
    </div>
  );
}

function PageView({ children, index }) {
  // position each page side-by-side based on it's index
  const position = `translateX(calc(${100 * index}%))`;

  return (
    <div
      style={{
        ...absoluteFill,
        transform: position,
        border: "thin solid red"
      }}
    >
      {children}
    </div>
  );
} 
```

现在我们要做的就是根据`activeIndex`属性:
更新内部容器的翻译值

```
function Pager({ children, activeIndex, size }) {
  // the total offset of the container div -- based on activeIndex
  const translateX = `translateX(${activeIndex * -100}%)`;

  return (
    <div ...>
      <div
        style={{
          ...absoluteFill,
          transform: translateX
        }}
      >
        {React.Children.map(children, (element, index) => (
          <PageView index={index} width={size}>
            {element}
          </PageView>
        ))}
      </div>
    </div>
  );
} 
```

内部容器现在通过计算其相对于`activeIndex`道具的偏移量来转移焦点。到目前为止，这是一个非常基本的实现，但是希望您可以看到它捕获了最终组件将做的核心行为。

接下来的步骤将是制作页面转换的动画，并处理手势以根据用户交互更新`activeIndex`。我们还将开放该组件的 API，使其在其他项目中的使用完全可控。

要查看完整示例，请访问[https://codesandbox.io/s/modern-moon-o5etr](https://codesandbox.io/s/modern-moon-o5etr)

在下一篇文章中，我们将制作页面之间的过渡动画:

[https://dev . to/ajsmth/building-a-pager-component-from-scratch-part-2-557 l](https://dev.to/ajsmth/building-a-pager-component-from-scratch-part-2-557l)