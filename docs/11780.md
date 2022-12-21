# 从头开始构建分页组件* -第 2 部分

> 原文：<https://dev.to/ajsmth/building-a-pager-component-from-scratch-part-2-557l>

如果你错过了本课的第一部分，可以在这里找到:[https://dev . to/ajs mth/building-a-pager-component-from-scratch-4 nlh](https://dev.to/ajsmth/building-a-pager-component-from-scratch-4nlh)

在这一部分中，我们将通过处理动画和手势在子视图之间翻页来添加已经创建的页面组件

我们将添加的第一件事是当`activeIndex`道具改变时的弹簧动画。为了做到这一点，让我们引入`react-spring`并导入它的一些功能:

```
yarn add react-spring 

import {animated, useSpring} from 'react-spring' 
```

添加 spring 页面过渡:

```
function Pager({ children, activeIndex, size }) {
  // the total offset of the container div -- based on activeIndex
  const translateX = `translateX(calc(${activeIndex * -100}%))`;

  // this will animate changes in activeIndex between pages:
  const animatedStyle = useSpring({ transform: translateX })

  return (
    <div ...>

      {/* Update to animated.div */}
      <animated.div
        style={{
          ...absoluteFill,
          // we will translate this container view to bring children into focus
          ...animatedStyle
        }}
      >
        {React.Children.map(children, (element, index) => (
          <PageView index={index} width={size}>
            {element}
          </PageView>
        ))}
      </animated.div>

    </div>
  );
} 
```

现在我们有了一个在页面变化之间转换的弹簧动画

接下来，我们希望添加对处理滑动手势的支持。同样，我们需要一个助手库

```
yarn add react-use-gesture

import {useDrag} from 'react-use-gesture' 
```

这将帮助我们跟踪容器视图上的拖动值:

```
function Pager({ children, activeIndex, size }) {
  // ...

  const [{ dx }, set] = useSpring(() => ({ dx: 0 }));

  const bind = useDrag(({ delta }) => {
    const [dx] = delta;
    set({ dx: dx });
  });

  const dragX = dx.interpolate(dx => `translateX(${dx}px)`);

  {/* Change this container to animated.div */}
  return (
    <animated.div
      {...bind()}
      style={{
        ...
        transform: dragX
      }}
    >
     {...}
    </animated.div>
  );
} 
```

您会注意到，在释放后，平移值需要重置，以便使视图重新居中。为了实现这一点，让我们更新我们刚刚编写的 useDrag()回调:

```
 const bind = useDrag(({ delta, last }) => {
    const [dx] = delta;
    set({ dx: dx });

    // last means they've released from dragging
    if (last) {
      set({ dx: 0 });
    }
  }); 
```

现在视图在发布后重新居中。

到目前为止，一切顺利。我们现在需要考虑的是用户拖动了多远，如果超出了某个阈值，我们就更新 activeIndex，这样下一个/上一个视图就成为焦点了。

我们要做的第一件事是确定何时应该改变的阈值——在我们的例子中，我将它设置为+/- 100:
的任意值

```
 const bind = useDrag(({ delta, last }) => {
    const [dx] = delta;
    set({ dx: dx });

    // last means they've released from dragging
    if (last) {
      if (dx > DX_THRESHOLD) {
        // transition to previous view
      }

      if (dx < -DX_THRESHOLD) {
        // transition to next view
      }

      set({ dx: 0 });
    }
  }); 
```

现在，我们可以使用回调属性来更新`activeIndex`属性，并正确聚焦上一页/下一页:

```
// add an onChange prop:
function Pager({ ..., onChange }) {

  ...

  // callback to onChange prop with new active value:

  const bind = useDrag(({ delta, last }) => {
    const [dx] = delta;
    set({ dx: dx });

    // last means they've released from dragging
    if (last) {
      if (dx > DX_THRESHOLD) {
        // transition to previous view
        onChange(activeIndex - 1)
      }

      if (dx < -DX_THRESHOLD) {
        // transition to next view
        onChange(activeIndex + 1)
      }

      set({ dx: 0 });
    }
  });

  ...
} 
```

我们能做的最后一件事是移除容器视图周围的边框(如果你的样式中还有它的话)并添加一个`overflow: hidden`样式，如果你想隐藏非焦点视图的话。

最后一点——在实践中，我们可能希望将阈值计算为总宽度的百分比，或者任何您认为最有效的值。

来源可以在这里看到:[https://codesandbox.io/s/intelligent-cache-5f366](https://codesandbox.io/s/intelligent-cache-5f366)

我们现在有了一个可维护的页面组件，它可以处理手势和动画页面转换

我们接下来要看的是开放分页 API 作为受控和非受控组件，以及子视图的伪虚拟化，这可能有助于应用程序的性能。我们还将看看在我们现有的实现中发生的一些问题