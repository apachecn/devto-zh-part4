# Web 组件和直接 HTML 一样适合 SEO？

> 原文：<https://dev.to/megazear7/web-component-as-good-as-direct-html-for-seo-3ccm>

我一直在思考 web 组件、光线 dom 与阴影 dom、服务器端渲染和 SEO。我想到的问题是，使用 web 组件来封装设计和功能而没有异步请求是否比直接在 light dom 中存储数据更不利于 SEO。也就是说，这些对 SEO 来说是好是坏:

```
<my-component title="Example"
              description="Some more content"
              button-text="Do something">
</my-component> 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
<div class="my-component">
  <h4>Example</div>
  <p>Some more content</p>
  <button>Do something</button>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

假设实现`<my-component>`的 web 组件同步创建与第二个例子相同的 html，就在影子 DOM 内部？

我们有第一种选择不如第二种选择对 SEO 有利的真实证据吗？

或者保守一点，因为我们不能确定爬行我们页面的服务实际上会运行 JavaScript，因此页面的有用内容会隐藏在属性中而不是语义 HTML 元素中？