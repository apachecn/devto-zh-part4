# 在与酶和 Jest 反应中测试 ScrollToTop 组件

> 原文：<https://dev.to/_builtbyjay/testing-the-scrolltotop-component-in-react-with-enzyme-and-jest-3p95>

如果你在一个长页面的应用程序中实现了 React Router，你肯定会注意到当位置改变时，滚动位置不会重置。

[React Training 推荐一个整洁的小组件](https://github.com/ReactTraining/react-router/blob/master/packages/react-router-dom/docs/guides/scroll-restoration.md)来包裹你的应用组件，它会在检测到位置变化时将滚动位置重置为 0:

```
import React from 'react';
import { withRouter } from 'react-router';
class ScrollToTop extends React.Component {
  componentDidUpdate(prevProps) {
    if (this.props.location !== prevProps.location) {
      window.scrollTo(0, 0);
    }
  }

  render() {
    return this.props.children;
  }
}

export default withRouter(ScrollToTop); 
```

为了确保组件做我们期望的事情，让我们为这个组件编写一些单元测试。

我们想测试一下:

1.  当位置改变时，组件用正确的参数调用`window.scrollTo`。
2.  它可以正确地呈现嵌套组件。

让我们建立我们的测试文件:

```
import React from 'react';
import { mount } from 'enzyme';
import { MemoryRouter } from 'react-router-dom';
import ScrollToTop from './ScrollToTop';
global.scrollTo = jest.fn();
describe('ScrollToTop', () => {
  let wrapper;
  let history;
  beforeEach(() => {
    wrapper = mount(
      <MemoryRouter initialEntries={['/']}>
        <ScrollToTop>
          <p>Hi</p>
        </ScrollToTop>
      </MemoryRouter>
    );
    history = wrapper.instance().history;
  });
  afterEach(() => {
    jest.clearAllMocks();
  });
}); 
```

首先，我们为`window.scrollTo`方法创建一个间谍。Jest 使用`global`作为`window`对象，因此我们通过将间谍分配给`global.scrollTo`来实现这一点。

我们在`MemoryRouter`中挂载我们的`ScrollToTop`组件，并获得对已挂载组件历史的引用。

然后，我们确保在每次测试后重置我们的 spy 方法。

设置完成后，我们就可以编写一些测试了！

```
it('calls window.scrollTo when route changes', () => {
  expect(global.scrollTo).not.toHaveBeenCalled();
  history.push('/new-url');
  expect(global.scrollTo).toHaveBeenCalledWith(0, 0);
}); 
```

我们调用`history.push`，就像我们在应用程序中一样。这将在`MemoryRouter`中激活一个路由变更，然后将更新的 props 传递给`ScrollToTop`组件，触发`componentDidUpdate`生命周期方法。

然后，我们可以断言，我们的 spy 方法是用正确的参数调用的。

最后，我们编写一个测试来确保`ScrollToTop`按照预期呈现它的嵌套组件。

```
it('it renders children', () => {
  const component = wrapper.find(ScrollToTop);
  expect(component.children().length).toEqual(1);
  expect(component.contains(<p>Hi</p>)).toEqual(true);
}); 
```

我们完了。我希望这对那些想测试对路由器事件做出反应的组件的人有用。