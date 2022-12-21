# 单元测试在 React 中呈现正确的组件

> 原文：<https://dev.to/maksimovicdanijel/unit-testing-render-prop-component-in-react-27hm>

## 什么是渲染道具？

渲染道具是一种在 React 生态系统中广泛使用模式。简而言之，渲染道具是一种将函数作为道具传递的模式，通常称为`render`或者更常见的是作为`children`道具。比如:

```
import React from 'react';

const RenderPropComponent = ({children}) => {
  const [counter, setCounter] = React.useState(0)  

  return children({counter, setCounter});
};

// usage
const Usage = () => {
  return (
    <RenderPropComponent>
      {({counter}) => <p>Counter: {counter}</p>}
    </RenderPropComponent>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

如果你想更广泛地了解 React 中的渲染道具模式以及它们在 React 生态系统中的用法，请查看这篇文章。

## 测试准备

为了测试渲染道具组件，我们应该先写一个！我们的组件将从 API 获取帖子，并向消费者组件公开加载状态和帖子。

```
import React from 'react';
import PropTypes from 'prop-types';

import { fetchPosts } from './api';

export default class FetchPosts extends React.Component {
  static propTypes = {
    children: PropTypes.func.isRequired
  };

  state = { posts: [], loading: false };

  async componentDidMount() {
    this.setState({ loading: true });

    const posts = await fetchPosts();

    this.setState({ posts, loading: false });  
  }

  render() {
    return this.props.children({posts: this.state.posts, loading});
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 写作测试

我们将使用`jest`和`react-testing-library`来编写我们的测试，但是如果您使用其他东西来编写测试，同样的原则也适用。

```
import React from 'react';
import { render } from 'react-testing-library';

import FetchPosts from './FetchPosts';

const mockPosts = [{ id: 1, title: 'Title' }];

jest.mock('./fetchPosts', () => Promise.resolve(mockPosts));

describe('FetchPosts component test', () => {
  it('should expose loading and posts prop', () => {
    const postsCallbackMock = jest.fn();

    const { getByTestId } = render(
      <FetchPosts>{postsCallbackMock}</FetchPosts>
    );

    expect(postsCallbackMock).toHaveBeenCalledWith({
      loading: false,
      posts: mockPosts
    })
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

这是测试渲染道具组件的一种简单方法。另一种方法是编写一个消费者组件，在页面上呈现一些内容，然后期望它与您收到的数据相匹配。例如:

```
import React from 'react';
import { render } from 'react-testing-library';

import FetchPosts from './FetchPosts';

const mockPosts = [{ id: 1, title: 'Title' }];

jest.mock('./fetchPosts', () => {
  return new Promise(resolve => {
    setTimeout(() => resolve(mockPosts), 100);
  });
});

const FetchPostsConsumer = () => (
  <FetchPosts>
    {({loading, posts}) => {
      if(loading) return <span data-testid="loading"></span>; 
      return posts.map(post => <p data-testid="post-title">{post.title}</p>)
    }}
  </FetchPosts> );

describe('FetchPosts component test', done => {
  it('should return correct loading and posts props', () => {
    const postsCallbackMock = jest.fn();

    const { getByTestId } = render(
      <FetchPostsConsumer />
    );

    expect(getByTestId('loading').textContent).toBe('Loading');

    setTimeout(() => {
      expect(getByTestId('post-title').textContent).toBe('Title');
      done()
    })
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

在这个测试的开始，我们声明我们的`fetchPosts`模块返回什么，这样我们可以在每个测试运行中有相同的结果(这些测试被称为确定性的)。这个函数的模拟版本正在解析一个承诺，但是在超时之后，这给了我们足够的时间在测试中检查加载状态。

接下来，我们声明一个组件，它使用了我们真正想要测试的 render prop 组件。组件呈现后，我们检查加载文本是否存在。一段时间后，我们将检查渲染属性回调是否会渲染正确的帖子。这种方法有点长，但在我看来，它给了我们一个更面向用户的测试，最终是用户将如何使用我们的组件。

## 结论

正如你所看到的，测试渲染组件并不困难。因为这种组件本身不会生成输出，所以我们必须在测试中提供缺失的部分，然后进行断言。一种更简单的方法是只提供一个模拟函数，并期望用正确的参数调用它。你更喜欢哪种方法？请在下面的评论中分享吧👇