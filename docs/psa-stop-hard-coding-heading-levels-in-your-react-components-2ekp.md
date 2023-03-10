# PSA:停止在 React 组件中硬编码标题级别

> 原文：<https://dev.to/s_aitchison/psa-stop-hard-coding-heading-levels-in-your-react-components-2ekp>

(这篇文章最初发表在[Up Your A11y-Heading Levels in Reusable Components](https://www.upyoura11y.com/reusable-components-with-headers/))

## 复用性是关键

React 如此受欢迎的一个关键原因是它能够定义一个组件，向它传递一些简单的道具，然后在各种地方重用它，而不必在整个应用程序中编写重复的 HTML。

当创建一个可重复使用的组件时，总有一些事情要考虑，例如，什么应该通过 props 定制，什么应该是组件本身不可分割的一部分。

## 标题层次不灵活的问题

考虑一个呈现标题和描述的简单卡片组件，它可能看起来像这样:

```
class SimpleCard extends React.Component {

  render() {
    const { title, description } = this.props;
    return (
      <div className='card'>
        <h2>{title}</h2>
        <p>{description}</p>
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

乍一看，我们有一个很好的可重用组件，我可以开始在我的应用程序中放置它。然而，我们有一个限制问题——“H2”元素。

### 标题具有语义价值，特别是对于屏幕阅读器

理解 HTML 中的标题级别不仅仅是标题文本的大小和样式，这一点很重要；它们提供了关于内容的组织和重要性的语义信息。

特别是，它们由屏幕阅读器解释，以便用户可以直接跳转到顶层标题、下一层标题等等。

因此，标题级别应该总是按照逻辑顺序增加，一次只能增加一步。这允许辅助技术的用户和视力正常的用户一样浏览你的内容。

### 我们的 SimpleCard 例子的问题

上面的 SimpleCard 组件定义了一个 h2 元素，只要我重用这个组件，它就会出现。这意味着我只能在已经有“h1”标题的页面上使用它，并且“h2”对我的页面流有逻辑意义。

鉴于 React 的强大之处在于组件的灵活重用，一些重构将是有益的。

## 通过道具中的标题关卡

这个问题可以通过一个简单的技巧轻松解决，这个技巧允许你根据传入的道具动态设置标题级别。参见升级版的简单卡:

```
class SimpleCard extends React.Component {

  render() {
    const { title, description, headingLevel } = this.props;
    const Title = headingLevel;
    return (
      <div className='card'>
        {title}
        <p>{description}</p>
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，组件现在在 props 中以字符串形式接收标题级别(例如“h1”)，并动态创建正确的标题元素以在卡片中呈现。请注意，在上面的示例中:

*   “Title”值可以是任何名称，我只是选择了“Title ”,因为它在上下文中有意义。如果这个值被称为‘Banana ’,我们渲染出`<Banana>{title}</Banana>`,这个例子仍然可以工作
*   “Title”是大小写的——这一点很重要，否则 React 不会将其识别为 DOM 元素。

## 最后的调整和考虑

虽然像这样基于字符串属性动态创建 DOM 元素非常强大，但是如果没有传入预期的属性类型，也会产生一些不必要的行为。

我建议在使用这种方法时，也要确保在尝试创建 Title 元素之前完成一些 props 验证。有多种方法可以实现这一点，但一个非常简单的实现可能是:

```
class SimpleCard extends React.Component {

  render() {
    const { title, description, headingLevel } = this.props;
    const validHeadingLevels = ['h1', 'h2', 'h3', 'h4', 'h5', 'h6'];

    const safeHeading = headingLevel ? headingLevel.toLowerCase() : '';
    const Title = validHeadingLevels.includes(safeHeading) ? safeHeading : 'p';

    return (
      <div className='card'>
        {title}
        <p>{description}</p>
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 现在组件可以在任何地方使用！

如果 props 中没有传递适当的标题级别，我们默认创建一个基本的段落元素。

太好了！因此，现在我可以在应用程序的不同位置使用 SimpleCard，而不会破坏任何语义 HTML 约定或降低屏幕阅读器用户的体验。

* * *

你觉得这篇文章有用吗？请考虑[给我买杯咖啡](https://www.buymeacoffee.com/mgkZuRU)，这样我就可以继续制作内容🙂