# 在与酶反应中检验 iframes

> 原文：<https://dev.to/jamescryer/testing-iframes-in-react-with-enzyme-31pe>

我最近做了一个不寻常的、有点过时的需求，在一个 [React.js](https://reactjs.org/) 应用程序中使用了一个 [iframe](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/iframe) 来展示一个营销/销售表单，同时还提供了一个无缝的 UX。集成使用 [postMessage](https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage) 进行跨域页面通信，这对单元测试来说是一个有趣的挑战。

下面的精简示例演示了 React [函数组件](https://reactjs.org/docs/components-and-props.html)和[回调引用](https://reactjs.org/docs/refs-and-the-dom.html#callback-refs)的使用。加载后，组件会向 iframe 中的页面发送一条消息。

```
function IframeComponent({ domain, path }) {
  let iframeRef;
  const post = (msg) => iframeRef.contentWindow.postMessage(JSON.stringify(msg), domain);
  const onIframeLoad = () => post({foo:'bar'});
  const getRef = (el) => iframeRef = el;
  return (
      <iframe
        ref={getRef}
        src={domain + path}
        onLoad={onIframeLoad}
      />
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

我想在不加载 iframe 的内容的情况下测试加载行为，我的解决方案(在互联网上做了大量研究之后)是通过访问并调用 ref 回调来对 iframe 使用 test double。

下面的测试写的是[摩卡](https://mochajs.org/)、[柴](https://www.chaijs.com/api/bdd/)、[酵素](https://airbnb.io/enzyme/)和[西农](https://sinonjs.org/)

```
describe('IframeComponent', () => {
  it('should invoke load message correctly', () => {
    const domain = 'https://my.domain.com';
    const wrapper = shallow(<IframeComponent domain={domain} path="/page"/>);
    const iframe = wrapper.find('iframe');
    const spy = mockIframePostMessage(iframe);
    const props = iframe.props();

    expect(props.src).to.be.equal(domain + path);
    expect(spy.called).to.be.equal(false);

    iframe.simulate('load');

    expect(spy.called).to.be.equal(true);
    expect(spy.args[0][0]).to.be.equal(JSON.stringify({ foo: 'bar' }));
    expect(spy.args[0][1]).to.be.equal(domain);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

下面展示了如何使用 Enzyme 找到 iframe React 节点并调用其 ref 回调，从而提供简单的 test double。

```
function mockIframePostMessage(iframe) {
  const postMessage = sinon.spy();
  const iframeMock = {
    contentWindow: {
      postMessage,
    },
  };
  iframe.getNode().ref(iframeMock);
  return postMessage;
} 
```

Enter fullscreen mode Exit fullscreen mode

> 注:我相信`getNode`现在在最新版本的酵素中已经被`getElement`取代了。

这种方法提供了一种在 React 中为 iframes 编写快速测试的好的、简单的方法🚀