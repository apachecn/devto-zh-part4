# 我最喜欢的使用 Flexbox 和 CSS 网格的 React 布局组件

> 原文：<https://dev.to/joelmturner/my-favorite-react-layout-components-using-flexbox-and-css-grid-hj6>

有几个组件是我在开发中几乎每天都会用到的。它们用于布局，由 flexbox 和 CSS 网格构成。在这个站点上，我用 MdxProvider 将这两者都传递下来，这样我所有的页面和帖子都可以很容易地访问它们。

## Flexbox

这是我最喜欢的一个。90%的情况下，使用给定的道具它都能完美工作。它可以很容易地通过样式组件、emotion 的`css`道具或主题 UI 的`sx`道具进行扩展，以满足我需要额外东西的少数情况。

这是构建 Flexbox 组件的一种自以为是的方式。这个想法是让规范的某些方面使用布尔值来帮助加速合成。

以下是它目前的道具列表:

```
type FlexboxProps = {
  className?: string;
  children?: React.ReactNode;

  inline?: boolean;
  vertical?: boolean; // column
  wrap?: boolean;
  noGrow?: boolean; // acts as no-grow and no-shrink
  grow?: number;
  shrink?: number;
  basis?: number | "auto";

  top?: boolean;
  middle?: boolean;
  bottom?: boolean;
  left?: boolean;
  center?: boolean;
  right?: boolean;
  between?: boolean;
  around?: boolean;
  gap?: boolean | number; // add margin between children similar to grid-gap on grid
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是一些用法的例子。请参见下面的 [CodeSandbox](#example) 了解更多信息。

```
<Flexbox vertical gap={3}>
  <Flexbox noGrow>
    <h1>A Title for You</h1>
  </Flexbox>
  <Flexbox bottom>
    <p>This is some copy to show how the box will fill the whole area but the text will be aligned bottom.</p>
  </Flexbox>
</Flexbox> 
```

Enter fullscreen mode Exit fullscreen mode

## 网格

Grid 是我的另一个最爱，它也非常符合我的需求。这个想法是使用 CSS 网格来创建布局。根据项目的不同，它可以与设计网格保持一致。

这可以与 Flexbox 配合使用，实现一些强大的布局。添加来自主题 UI 的`sx`道具，获得一些精彩的[断点牛逼](https://theme-ui.com/theme-spec#breakpoints)。

我们来看看道具。

```
type GridProps = {
  className?: string;
  children: React.ReactNode;
  gap?: string | number;        // grid-gap
  columns?: string | string[];  // grid-template-columns
  rows?: string | string[];     // grid-template-rows
} 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个使用它的例子。在 [CodeSandbox](#example) 中查看更多示例。

```
<Grid columns="1fr 1fr" gap="2">
  <img src="myImage.png" />
  <img src='catsSinging.png' />
</Grid> 
```

Enter fullscreen mode Exit fullscreen mode

## 例子

[https://codesandbox.io/embed/react-layout-components-flexbox-grid-d9e1u](https://codesandbox.io/embed/react-layout-components-flexbox-grid-d9e1u)

我曾经使用过一个接受不同跨度量的列组件，但是我最终没有使用它那么多。我倾向于通过`sx`道具或者用样式化的组件扩展来给孩子添加`span`。

我很想在这里了解一下人们用来使排版更快更容易的其他布局组件。如果你有你喜欢的，请让我知道。谢谢！