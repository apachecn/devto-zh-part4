# 为什么我使用反应测试库而不是酶

> 原文：<https://dev.to/kewah/why-i-m-using-react-testing-library-instead-of-enzyme-5a4j>

[酶](https://airbnb.io/enzyme/)自 2016 年以来一直是我测试 React 组件的首选武器。我最喜欢的是在使用浅层渲染测试时对组件的隔离。我可以专注于测试组件行为，并检查正确的道具是否传递给了子组件(主要使用快照测试)。这允许我不必模仿子组件，例如根组件，并且让测试快速运行。所以，我只在单元级别测试，每个组件单独测试，并使用 [Cypress](https://www.cypress.io/) (端到端测试)测试大图。

直到我开始用 React 16.8(“带钩子的那个”)的时候，我才看了看其他的测试库。主要原因是不能用酶测试定制钩子，并且不能使用浅渲染。这让我对其他解决方案更加好奇，并最终钻研了 [react-testing-library](https://testing-library.com/docs/react-testing-library/intro) 。

## 与酶的问题

当使用 Enzyme 渲染一个组件时，它会包装该组件，并允许我们使用丰富的 API 遍历树并访问组件的数据(实例、道具、状态、子组件等)。但是，强大的能力意味着巨大的责任，能够访问组件的内部状态(`.state()`、`.setState()`、`.instance()`)通常会导致对其实现的测试。我在处理 React 代码库时多次看到它，有时是因为它“比必须重现用户步骤更容易、更快”。例如，在一个有按钮的组件中，我用来搜索一个`Button`组件并调用它的`onClick`属性，而不是安装并模拟在那个元素上的点击。

```
wrapper
  .find('Button')
  .at(0)
  .prop('onClick')(); 
```

但是，如果我们用具有类似功能的组件替换`Button`，或者如果我们决定在不同的索引处移动按钮，测试就会中断。使用这种类型的测试会变得非常复杂，因为每次您接触到实现时，测试都会中断。这让重构变得非常痛苦。这本身并不是酶的错误，但是允许开发人员访问私有属性允许他们走捷径并测试实现细节，这导致了脆弱的测试。

## 不同的做法

反应测试库有一种不同于酶的测试方法。它比单元测试更接近集成测试。它呈现组件并将其附加到 DOM。我们只能访问 DOM 中的元素。没有内部状态，没有实例方法，只有用户可以与之交互的东西。这是最重要的，我们希望确保我们的代码在到达用户手中时不会崩溃。它帮助我重新思考在 React 组件上工作时，我应该如何测试以及测试什么。

到目前为止，这是一次很棒的经历:

*   我喜欢渲染只有一种类型，不需要考虑；
*   测试运行速度仍然很快。我错误地认为在每次测试之间在 DOM 上呈现组件会更昂贵；
*   DOM utils 关注于可访问性(`getByRole`、`getByAltText`等)；
*   我减少了对快照测试的依赖，测试了更多的单个元素( [jest-dom](https://github.com/testing-library/jest-dom) 很方便)，所以当我更改一个不相关的节点元素时，测试中断的情况会更少；
*   我的测试结构非常类似于我过去用设置工厂对酶所做的。

但是，它也有一些缺点，例如，如果它在挂载时获取数据，就必须模仿子依赖关系。所以根据需要，我可能会使用`jest.mock`来模仿一个子组件。

```
import { render } from '@testing-library/react';
import React from 'react';
import App from './App';
import SomeAppSection from './SomeAppSection';

jest.mock('./SomeAppSection');

SomeAppSection.mockReturnValue(<div>SomeAppSection</div>); 
const setup = (props = {}) => render(<App logout={jest.fn()} {...props} />); 
it('renders SomeAppSection', () => {
  const logout = jest.fn();
  const { getByText } = setup({ logout });

  getByText('SomeAppSection');
  expect(SomeAppSection).toHaveBeenCalledWith({ logout }, {});
}); 
```

在这个例子中，我不想处理`SomeAppSection`行为，因为这需要我模仿太多的东西，或者跟踪组件将来什么时候会改变。所以，我退回到我过去用酶做的事情，嘲笑它。

与测试相关的优秀读物——图书馆或一般测试:

*   [测试实施细节](https://kentcdodds.com/blog/testing-implementation-details)
*   [如何知道要考什么](https://kentcdodds.com/blog/how-to-know-what-to-test)
*   [每个单元测试必须回答的 5 个问题](https://medium.com/javascript-scene/what-every-unit-test-needs-f6cd34d9836d)
*   [避开测试用户](https://kentcdodds.com/blog/avoid-the-test-user)