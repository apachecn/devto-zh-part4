# 创建具有 15 个元素的无限卷轴

> 原文：<https://dev.to/dhilipkmr/creating-infinite-scroll-with-15-elements-4dg4>

[![](img/767de7185bf52e8646dd733054692bb6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eg18Hktv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wyovrb8mub735ozgohgi.png)

*无限滚动*是我们预先加载结果的一部分，当到达加载列表的末尾时，我们加载下一组结果，依此类推。

## 那么问题是什么呢？

假设有一个包含 10，000 个元素的列表，假设每个元素都显示在一个`<li>`标签中。因此，当我们到达列表的末尾时，将有 10，000 个`<li>`节点连接到父节点。

如果复杂的`<li>`中有更多的孩子，这将对网站的性能造成巨大的影响，也会影响网页的滚动性能。

## 如何克服这种超载的 DOM 大小？

*   这可以通过*卸载*或*丢弃*不属于用户视口的顶部`<li>`来克服。
*   (即)当用户向下滚动时，我们开始添加新行，并应删除折叠上方的现有行。
*   我们将通过固定的孩子大小来实现这一点，比如最多 15 行，我们将为新的输入集更新同样的大小，从而保持较低的 DOM 大小。

## 要考虑的事情:

*向列表底部添加新节点应该在列表的最后一个元素进入视区时完成。将新元素添加到 DOM 后，应删除现有元素，以便删除的元素位于文件夹上方。(即在用户的视口之外)。
*当用户向上滚动并到达顶部元素时，我们应该加载已删除的顶部元素，并删除位于视窗下方的底部元素。

## 我们要实现什么？

一个组件，可以使用 15 个元素的固定窗口大小呈现 n 个项目的列表。(即)在任何时候，即使无限滚动浏览`n`元素，也只会出现 15 个 DOM 节点。

### 输出:

[![](img/741ba84bcd2b3d46ff1f5237c913999e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--shFAdfwM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/flsqzrldjuu2eac1xrmo.gif)

# 接近:

*   我们将从 CSS 那里得到一些帮助来实现这个目标。(我们会利用 CSS 的绝对定位)。

*   包装所有元素的父 div 将位于`relative`位置。

*   我们所有的孩子都将被定位在相对于父 div 的位置。

*   我们为每个孩子计算`top`，并将其应用到`<li>`的样式参数中。

*   在任何给定的时间，我们将在 DOM 中最多维护 15 个`<li>`元素。

注意:为了简单起见，我们将只考虑固定大小的`<li>`情况。

# 实现:

## 初始化:

```
import React from "react";
const THRESHOLD = 15;

class SlidingWindowScroll extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      start: 0,
      end: THRESHOLD
    };
    this.$topElement = React.createRef();
    this.$bottomElement = React.createRef();
  }
} 
```

*   我们已经创建了我们的`SlidingWindowScroll`组件，它有一个包含`start`值和`end`值的状态。
*   `start`是列表数组的起始索引，它必须在滚动时被延迟加载。
*   `end`是列表数组的最后一个索引。
*   `THRESHOLD`给出了应该出现在 DOM 中的`<li>`元素的最大数量。
*   我们创建两个引用:

    *   `this.$topElement`，将指向列表项中的第一个元素(第 0 个索引)。
    *   `this.$bottomElement`，将指向列表项中的最后一个元素(第 14 个索引)。
*   每当添加或删除新元素时，必须相应地更新 refs，以指向当前呈现的列表的顶部和底部。

## 渲染:

```
 getReference = (index, isLastIndex) => {
    if (index === 0)
      return this.$topElement;  // Attach this ref for first element
    if (isLastIndex) 
      return this.$bottomElement; // Attach this ref for last element
    return null;
  } 
```

```
render() {
    const {list, height} = this.props;
    const {start, end} = this.state;
    const updatedList = list.slice(start, end);
    const lastIndex = updatedList.length - 1;
    return (
      <ul style={{position: 'relative'}}>
        {updatedList.map((item, index) => {
          const top = (height * (index + start)) + 'px';
          const refVal = this.getReference(index, index === lastIndex);
          const id = index === 0 ? 'top' : (index === lastIndex ? 'bottom' : '');
          return (<li className="li-card" key={item.key} style={{top}} ref={refVal} id={id}>{item.value}</li>);
        })}
      </ul>
    );
  } 
```

*   我们从`props`得到`list`和`height`，从`state`得到列表中的`start`和`end`。
*   `updatedList`给出要渲染的元素的新集合。
*   `<ul>`被制成`relative`。
*   对于列表中的每一项，我们计算它相对于其相对父项的`top`位置。
*   它是通过当前项在`list`(索引+开始)中的位置乘以每个元素的`height`计算出来的。
*   `refVal`给出必须附加的`ref`。在索引 0 的情况下，它将引用`this.$topElement`，在最后一个索引的情况下，它将引用`this.$bottomElement`。
*   我们为第一个元素附加值为`top`的`id`，为最后一个元素附加值为`bottom`的 id。

## 为裁判设置观察员:

```
 componentDidMount() {
    this.intiateScrollObserver();
  }

  componentDidUpdate(prevProps, prevState) {
    if ((prevState.end !== this.state.end) || (prevState.start !== this.state.start)) {
      this.intiateScrollObserver();
    }
  } 
```

*   在`Mounting`上，当`start`或`end`的值改变时，指向渲染的`<li>`顶部和底部的`ref`也改变。
*   由于`ref`开始指向不同的元素，我们将不得不监听这两个 refs 来知道它们何时进入视口。

我们使用`IntersectionObserver`来识别`root`或`bottom`元素是否在视口中。

```
intiateScrollObserver = () => {
    const options = {
      root: null,          // To listen to window scroll
      rootMargin: '0px',   // if there is any margin associated with it
      threshold: 0.01      // if 1% of the element is in view
    };
    this.observer = new IntersectionObserver(this.callback, options);
    if (this.$topElement.current) {
      this.observer.observe(this.$topElement.current);
    }
    if (this.$bottomElement.current) {
      this.observer.observe(this.$bottomElement.current);
    }
  } 
```

*   我们用一个`callback`来创建我们的`IntersectionObserver`，当元素进入和离开视口和`options`时，这个`callback`应该触发
**   在`options`中，我们指定我们正在监听窗口中的滚动事件，并且该元素应该被标记为可见，即使该元素的 1%进入视图(通过`threshold`键)。*   然后，我们观察两个 ref(`this.$topElement`和`this.$bottomElement`)以了解它何时进入/离开视口。*

 *## 处理`<li>`的视口入口

```
callback = (entries, observer) => {
    entries.forEach((entry, index) => {
      const listLength = this.props.list.length;
      const {start, end} = this.state;
      // Scroll Down
      // We make increments and decrements in 10s
      if (entry.isIntersecting && entry.target.id === "bottom") {
        const maxStartIndex = listLength - 1 - THRESHOLD;     // Maximum index value `start` can take
        const maxEndIndex = listLength - 1;                   // Maximum index value `end` can take
        const newEnd = (end + 10) <= maxEndIndex ? end + 10 : maxEndIndex;
        const newStart = (end - 5) <= maxStartIndex ? end - 5 : maxStartIndex;
        this.updateState(newStart, newEnd);
      }
      // Scroll up
      if (entry.isIntersecting && entry.target.id === "top") {
        const newEnd = end === THRESHOLD ? THRESHOLD : (end - 10 > THRESHOLD ? end - 10 : THRESHOLD);
        let newStart = start === 0 ? 0 : (start - 10 > 0 ? start - 10 : 0);
        this.updateState(newStart, newEnd);
      }
    });
  } 
```

*   每当`this.$topElement`或`this.$bottomElement`进入或离开视窗时，`callback`就会被调用。
*   `entries`是一个按照创建顺序排列所有观察点的数组。
*   `entries`在我们的例子中会有`this.$topElement`和`this.$bottomElement`。
*   `isIntersecting`属性给出了元素是否在视窗中，而`id`帮助我们决定它是进入视图的底部元素还是顶部元素。
*   我们计算在`state`的`start`和`end`之间保持 15 个元素。
*   我们添加和删除 10 个项目，并确保至少有 15 个元素存在。
*   最后，我们用`start`和`end`的新值更新`state`。

## 更新状态:

```
resetObservation = () => {
    this.observer.unobserve(this.$bottomElement.current);
    this.observer.unobserve(this.$topElement.current);
    this.$bottomElement = React.createRef();
    this.$topElement = React.createRef();
  }

  updateState = (newStart, newEnd) => {
    const {start, end} = this.state;
    if (start !== newStart || end !== newEnd) {
      this.resetObservation();
      this.setState({
        start: newStart,
        end: newEnd
      });
    }
  } 
```

*   我们用新值设置了`state`,并且重置了所有的观察器。
*   复位时，所有的观察者都应该`unobserve`不去观察它以后的变化。我们为`this.$bottomElement`和`this.$topElement`创建一个新的`ref`。

现在在 scroll 上我们一次只有 15 个元素，但是给用户一种在 DOM 中拥有所有元素的感觉。

拥有一个唯一的键，以避免重复呈现相同的`<li>`。

### 输出:

[![](img/741ba84bcd2b3d46ff1f5237c913999e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--shFAdfwM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/flsqzrldjuu2eac1xrmo.gif)

[![](img/e1fe3901940e8b1f1fb73159a8e8c5c0.png)](https://i.giphy.com/media/9Jcw5pUQlgQLe5NonJ/giphy-downsized-large.gif)

关注我有趣的内容。

[![Edit zealous-firefly-5r7ie](img/0b3f0135583496627e3621355d8e9248.png)](https://codesandbox.io/s/zealous-firefly-5r7ie?fontsize=14)

[回购链接](https://github.com/dhilipkmr/intersection_observer)
我的[网站](https://www.dhilipkmr.dev)，[博客](https://dev.to/dhilipkmr)和[推特](https://twitter.com/dhilipkmr_)

那都是乡亲们！*