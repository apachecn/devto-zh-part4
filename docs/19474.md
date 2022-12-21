# 改进无限滚动组件上的触摸事件

> 原文：<https://dev.to/wesleylhandy/improving-touch-events-upon-an-infinite-scrolling-component-kg7>

[![Closeup of a butterfly sitting on the tip of the someone's index finger](img/fd8265bf50cbce7950f62e0cd107a3c1.png)](/static/9262257e3e1dba7123eb7236d14248d8/de376/butterfly-finger.jpg)

在我最近关于使用 React Hooks 设置无限滚动的博文中，我创建了一个可以在桌面和触摸屏环境下工作的无限滚动版本。然而，当我把它投入生产时，我遇到了一个我没有预料到的问题，**我页面上的一些链接停止工作**。我试着调试我的 CSS，但无济于事，只是意识到解决方案包括用一个非常简单的补丁更新我的`touchend`事件处理程序。

## 解决方案:调试 CSS？？

我的组件的结构在块级元素中嵌套了一些链接。为了实现无限滚动，我映射了一个企业列表，如下所示:

```
<li key={govId} index={idx + 1}>
  <h2>
    <Link 
      to={ `/businesses/${businessName}` } state={{ 
        prevPath: typeof window !== `undefined` ? window.location.pathname : ''
      }}
    >
      {businessName}
    </Link>
  </h2>
  <p>
    <a href={`tel:${businessPhone}`}>{businessPhone}</a>
  </p>
</li> 
```

根据过去的经验，我立即想到解决方案是调整嵌套链接的`z-index`,或者在父元素上设置`pointer-events`。我都试过:

```
 li, li>h2, p { 
        pointer-events: auto;
        z-index: 1;
    }
    li>h2>a, p>a {
        z-index: 3;
    } 
```

这两种解决方案都没有解决问题，也不是问题的开始，至少在我的实现中是这样。

## 解决方案:调试`touchend`事件处理程序！！

首先，这是我为这个组件编写的代码的初始状态。我定义`handleScroll`向无限卷轴中添加更多的条目。然后，`handleTouchEnd`调用 scroll 事件处理程序来避免双重加载。特别注意`e.preventDefault`(也许这根本不应该被称为？我们很快就会知道了):

```
 const handleScroll = () => {
    if ( !hasMore ) return;
    if ( window && 
       ( window.innerHeight + 
         document.documentElement.scrollTop >=
         document.documentElement.offsetHeight ) 
    ) {
      loadMore() // function to add more items to the infinite scroll until no items left
    }
  }

  const handleTouchEnd = (e) => {
      e.preventDefault(); handleScroll();
  }

  useEffect(() => {
    window && window.addEventListener('touchend', handleTouchEnd)
    window && window.addEventListener('scroll', handleScroll)
    window && window.addEventListener('resize', handleScroll)
    return () => {
      window && window.removeEventListener('touchend', handleTouchEnd)
      window && window.removeEventListener('scroll', handleScroll)
      window && window.removeEventListener('resize', handleScroll)
    };
  }, [businesses, hasMore]) 
```

在意识到更新我的`css`不能解决我的问题后，我想知道到底是什么导致了我的问题。在这个特定的组件中，我使用两个`React`钩子来管理状态和处理事件- `useState`和`useEffect`。我想知道，

> 好的，这是一个`Gatsby`项目，我正在从 gatsby 库中导入`Link`组件。这些钩子会干扰`Link`的功能吗？

这不是问题所在。我可以在控制台中看到,`Link`呈现了一个简单的`a`标签，其他的`Link`组件也在页面上工作，比如那些由`Navigation`组件呈现的组件。唯一不起作用的链接是在我的无限滚动列表组件中。此外，这些链接在桌面环境下工作得非常好。所以我再次想知道，

> 为什么这在桌面上行得通，而在移动设备上却行不通？没有抛出任何错误。如果我将属性粘贴到浏览器中,`href`是有效的并且可以工作。`touchend`事件又是如何工作的呢？

这让我开始研究触摸屏触发事件的顺序。

### `click`跟在`touchend`之后

根据 MDN 的说法，W3C 标准要求触摸屏触发的[事件的*典型*顺序如下:](https://developer.mozilla.org/en-US/docs/Web/API/Touch_events/Supporting_both_TouchEvent_and_MouseEvent#Event_order)

*   `touchstart`
*   零个或多个`touchmove`事件，取决于手指的移动
*   `touchend`
*   `mousemove`
*   `mousedown`
*   `mouseup`
*   `click`

还记得我让你特别注意我在`touchend`事件中给`e.preventDefault()`打电话吗？原来那是罪魁祸首。通过取消对`touchend`上更多事件的调度，链接组件的`click`事件从未被触发。正如 MDN 告诉我们的:

> 如果`touchstart`、`touchmove`或`touchend`事件在交互过程中被取消，*将不会触发*鼠标或点击事件。

解决方案必须包括不调用`e.preventDefault`，特别是当一个链接是`touchend`的目标时。

因此，唯一需要做的修改是在我的`handleTouchEnd`函数中添加一个条件，检查`a`标签或`Element.tagName == "A"`，如果目标不是这样一个标签，只调用`e.preventDefault()`:

```
const handleTouchEnd = (e) => {
  if (e.target.tagName !== "A") {
    e.preventDefault(); 
    handleScroll();
  } else {
    console.log("this makes me a click event, most likely")
  }
} 
```

> 艾米·汉弗莱斯在 [Unsplash](https://unsplash.com/) 上的照片