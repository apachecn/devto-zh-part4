# 以正确的方式测试有状态 React 组件

> 原文：<https://dev.to/chalarangelo/writing-tests-for-stateful-react-components-the-right-way-3ke2>

最近，为了熟悉代码库和组件的用法，我的任务是为一个项目的几个 React 组件添加测试。开始时是一个简单的任务，最终却有了一个发现！同事指出我之前没想过的事情的瞬间。

项目和组件的细节并不重要，但是关键的细节是我正在为一个更大的项目的一部分并且经常被编辑的**有状态反应组件**编写测试。我最初的方法包括编写一些基本的测试，比如检查组件是否正确呈现和/或某些事件是否正确触发。

然而，作为我测试的一部分，我是直接检查一个组件的状态。以任何人的标准来看，这都不是一种罪过，但是对于一个有许多可移动部分的代码库来说，这并不是最棒的想法。让我给你举个例子。

考虑下面的测试:

```
context('the component is initialized in a collapsed state', function() {
  let wrapper;
  beforeEach(function(){
    wrapper = mount(<StatefulComponent />);
  });

  it('component state.expanded is false', function() {
    expect(wrapper.state('expanded')).to.be.false;
  });
}); 
```

在这个例子中，我们检查组件的状态是否等于。只要这个简单的条件成立，我们的测试就会通过。这是一个非常简单的测试，即使对于完全不熟悉代码库的人来说也应该很容易理解。

但是，随着时间的推移，组件的实现可能会发生变化。如果`expanded`在我们州最终有了不同的含义，会发生什么？或者更糟的是，如果它没有以同样的方式反映在界面上呢？

进入*尤里卡！*时刻:

> 应用程序的 UI 应该总是被认为是组件的属性和状态相结合的结果。

这意味着我们应该在测试时将组件的状态视为一个抽象的概念，很像神经网络的隐藏层，并避免直接检查它。因此，我们应该做一些更像这样的事情，而不是上面介绍的测试:

```
context('the component is initialized in a collapsed state', function() {
  let wrapper;
  beforeEach(function(){
    wrapper = mount(<StatefulComponent />);
  });

  it('component does not have the expanded class', function() {
    expect(wrapper.find('div').hasClass('expanded')).to.be.false;
  });
}); 
```

我们的测试仍然易于阅读和理解，但总的来说是一个更好的测试。

通过直接检查 DOM 而不是组件的状态，我们通知下一个**处理这个组件的人在特定环境下组件应该呈现什么**，而不是强迫他们使用**一种特定的方式来做那件事**。这是记录组件的一种更好的方法，而且如果有人以改变组件的 DOM 表示的方式重构 UI，就更容易看到测试本身发生了什么变化。

* * *

感谢你阅读这篇文章。如有任何反馈或问题，欢迎在下方留言。如果你认为这是一篇有趣的文章，请关注我，分享更多信息。