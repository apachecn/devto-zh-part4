# 连接你的用户界面组件

> 原文：<https://dev.to/kevtiq/interfacing-your-ui-components-5c52>

这几年，前端开发成了我生活中重要的一部分。但是当我几年前开始的时候，我不知道 API 是什么。我和他们一起工作，但是我从来不关心它到底是什么，或者建造它需要什么。我知道 UI 中接口的概念，但是我不知道它和 API 的字母“I”的关系。在某一点上，协作变得更加重要。你的同事应该能够使用和理解你的工作。这就是我开始看到前端开发中 API 和 UI 之间的联系的原因。

## 什么是接口？

作为一名前端工程师，始终要考虑到工作的可重用性。另一方面，我们的工作也应该对用户是可用的和可访问的。这两个概念与现代工作方式结合在一起，在现代工作方式中，设计系统是关注的中心。正如 Alla Kholmatova 在她的书中所描述的，设计系统由可重用的模式组成。但是，如何使某些东西可重用，尤其是当内容模式本身相当复杂的时候？

这就是接口概念发挥作用的地方。非常值得信赖的维基百科对接口的定义如下。

> 接口是共享的边界，计算机系统的两个或多个独立组件通过它交换信息

用我的前端护目镜看这个定义的时候，直接看到了*这个词的成分*。两个或多个独立的 UI 组件协同工作正是我们创建大多数设计系统的方式。例如，在 [React](https://reactjs.org/docs/components-and-props.html) 中，您通过子组件的*道具*将数据从父组件提供给子组件。那么这是前端开发中我们设计和开发界面的地方吗？是的，是的，它是。

如前所述，这不是接口发挥作用的唯一地方。当用户点击一个按钮，或者填写并提交一个表单时，他或她正在与我们的一个(或多个)组件进行交互。用户与之交互的 UI 是界面定义的共享边界。用户的交互是向我们的组件传递他或她的意图的一种方式。

## 组件解剖

因此，在设计和开发组件时，我们要处理两种类型的接口。通过组合这些组件，我们可以创建一个用户可以用来连接到我们的系统的 UI。太好了！完事了吗？不完全是。当我们在一个接口中改变一些东西时，它会影响同一个组件的另一个接口。为了更好地理解这一点，我们必须看看组件的结构。

[![The UI component anatomy](img/97b6867e653eb1cadcc2f8314c8111b3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eNHK-K7N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kevtiq.co/img/ui-component-anatomy.png)

如您所见，UI 组件由几个相互交互的部分组成。当用户通过单击按钮与 UI 交互时，组件内部会触发一些逻辑。根据不同的逻辑，组件中可能会发生几种情况。组件的内部状态得到更新，我们向后端发送请求，或者向用户返回信息。但是组件内部缺少一条重要的路径。通过它的 API，它可以向其他组件提供信息。这只有在其他组件通过提供回调函数(例如按钮组件的`onClick`函数)连接到您的组件时才有效。

您的组件可以通过其他人的 API 向他们提供信息，反之亦然。另一个组件可以通过 API 向您的组件提供信息。这是其他工程师用的接口。当另一个组件通过 API 连接时，我们的组件运行一些逻辑。根据逻辑，它要么更新其内部状态，提供信息，要么根据信息更新 UI。

在最后一种情况下，是我们的组件在其 API 中描述它如何与其他组件连接。它描述了它可以接收什么类型的信息，也描述了它何时可以提供信息(例如像`onClick`这样的回调函数)。我们可以经常假设其他工程师不知道我们 UI 组件的内部结构。因此，界面成为一种方式来描述我们希望别人如何使用我们的工作并与之互动。但是，我们如何描述我们的界面，以确保其他人知道他们应该如何与他们互动？

> 界面成为一种描述我们希望他人如何使用我们的作品并与之互动的方式

## 描述接口

通过适当的设计，这个问题已经部分地为你的用户解决了。向用户提供良好的可视化队列，让他们知道在哪里以及如何与您的组件进行交互，这是一个良好的开端。第二步是实现设计。不是每个用户都像你想象的那样与 UI 交互。这可能有各种各样的原因，但一个很大的原因可能是残疾。当用户半盲时，他或她可能会使用屏幕阅读器与您的组件进行交互。UI 的设计不必改变，但是在实现层面上，考虑这些用例。这就是所谓的[可达性](https://www.smashingmagazine.com/category/accessibility/)(或`a11y`)领域。

然而，在这篇文章的其余部分，我想讨论工程师的界面或 API。描述我们希望其他工程师如何与我们的 UI 组件交互并不是一项简单的任务。作为一名工程师，包括我自己，我们经常觉得我们的工作是不言自明的。它不是。我们必须描述至少一些东西，以确保不同水平的工程师可以使用我们的工作，如果我们想的话。

*   他们可以访问我们的 UI 组件的哪些 APIs
*   对于每个 API，他们如何使用它以及它的目的是什么(例如，描述他们如何影响你的 UI 组件的样式)；
*   显示实际结果(UI)和不同 API 输入组合的影响的示例。

你可以通过各种方式实现以上目标。您可以在 markdown ( `.md`)文件中编写大量文档(例如`README.md`)。一个有趣的选择是建立一个文档网站。在那里你可以和组件互动。如果这需要太大的投资，使用像 [Gitbook](https://www.gitbook.com/) 或 [Storybook](https://storybook.js.org/) 这样的工具是广泛记录 UI 组件的好技术，投资很低。

## React 的 API 指南

直到现在，都是文字很多，例子太少(我的错，对不起)。因此，让我们讨论一些使用 React 描述 API 的要点。希望您看到这些例子也可以应用于其他框架。在 React 中，你的 API 就是你定义的 [*道具*](https://reactjs.org/docs/components-and-props.html) 。让我们看一个带有一些属性的小按钮示例。

```
const Button = ({ onClick, variant, children, override, className, type }) => {
  return (
    <button
      onClick={onClick}
      type={type}
      className={`${override.defaultClassName}  ${className}`}
      data-variant={variant}>
      {children}
    </button>
  );
};

Button.propTypes = {
  variant: PropTypes.oneOf(['primary', 'stroke', 'flat']).isRequired,
  onClick: PropTypes.func.isRequired,
  override: PropTypes.object
};

Button.defaultProps = {
  variant: 'primary',
  className: '',
  override: {
    defaultClassName: 'btn'
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

在顶部，我们看到实际的组件。在`return`语句中，我们可以看到正在生成的 UI，但是我们也可以看到如何应用*道具*。更重要的是在这种情况下的`Button.propTypes`和`Button.defaultProps`。前者是 React 中描述我们期望每个*道具*的值的类型的一种方式，以及它们是否是必需的。对于`variant` *属性*，我们还看到我们限制了它可以拥有的值。

用`defaultProps`我们定义了组件使用的一些默认值。当有人使用我们的组件时，使用默认值是避免不必要的副作用的好方法。当你不定义`className`时，你会得到`undefined`作为结果。但是因为设置了一个空字符串的默认值，这个空字符串将被用来代替`undefined`。当有人创建一个名为 undefined 的 CSS 类时，这可以避免潜在的副作用。

看起来很奇怪的*道具*之一是`override`。假设您为按钮使用一个默认的类名`.btn`。虽然这是一个明智的好名字，但是从事不同项目的其他开发人员可能会使用不同的默认类名。在`override` *道具*中你可以覆盖一些通常使用的默认内部变量。理想情况下，它很少被使用，但是它是一种使您的组件更强大以供他人使用的简单方法。但是作为开发人员，您不希望每次都设置`override.defaultClassName`。在这种情况下，您可以创建一个包装我们的`Button`组件的新组件。这避免了其他开发人员需要知道我们组件的内部逻辑。

```
const PrimaryButton = (props) => (<Button
 variant="primary"
 override={{ defaultClassName='company-btn' }}
 {...props}
/>); 
```

Enter fullscreen mode Exit fullscreen mode

## 现在怎么样了？

连接你的组件很难。使用您的 UI 组件的其他开发人员可能对它的内部不感兴趣。尽管如此，还是要确保他们意识到如何使用它并与之互动。最终，它们会影响用户界面，UI。用户还需要了解他们如何与我们的组件交互。当试图实现这一点时，从小处着手(例如 API 的命名约定)。从那里，你可以扩展并找到比这篇文章中描述的更好的接口方式。

*本文原载于[kevtiq.co](https://kevtiq.co/blog/interfacing-your-ui-components/)T3】*