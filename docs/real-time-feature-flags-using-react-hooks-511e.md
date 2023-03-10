# 使用 React 挂钩的实时特征标志

> 原文：<https://dev.to/martijndeh/real-time-feature-flags-using-react-hooks-511e>

你正在开发一个还没有准备好的功能吗？但是仍然想向特定的用户群展示它？你想跳过那个可怕的叫做临时环境的东西吗？不要再看了！

你并不孤单。越来越多的团队正在脱离他们的阶段环境，仅仅简单地使用特性标志来控制为哪些用户启用一个特性。**登台只会增加您的功能交付的延迟。**

```
const MyComponent = () => {
  const { isEnabled } = useBoolean(`my-feature`);

  if (isEnabled) {
    return (
      <div>
        // New component here
      </div>
    );
  }

  return (
    <div>
      // Old component stuff here
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

这篇文章是专门关于 React 的，但是我们有不同的客户端库。[在布尔](https://useboolean.com/)查看列表。

开始使用特性标志就是这么简单。将提供者组件添加到组件树的根中，这样就完成了。你可以在布尔注册并创建一个[免费账户。](https://useboolean.com/) 

```
<BooleanProvider apiKey={process.env.BOOLEAN_API_KEY}>
  {/* Your component tree here */}
</BooleanProvider> 
```

Enter fullscreen mode Exit fullscreen mode

当`BooleanProvider`挂载时，库启动一个 WebSocket 连接来获取特性的初始状态。当您在布尔仪表板中切换您的特性时，更改会通过这些 WebSocket 连接推送给您的所有用户。立刻。

## 高级瞄准

还有呢！使用布尔，每个功能，您可以针对特定用户。所有这一切都发生在没有布尔接收到用户数据副本的情况下，因为规则是在库中评估的。我们认为这种隐私第一的方法非常重要。

```
// Prop drilling is just an example here, you can also use a hook or any other way to get the user data in
const MyComponent = ({ user }) => {
  const { isEnabled } = useBoolean(`my-feature`, user.id, {
    // Add whatever attributes you want here
    plan: user.plan,
  });

  if (isEnabled) {
    return (
      <div>
        // New component here
      </div>
    );
  }

  return (
    <div>
      // Old component stuff here
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，在布尔仪表板中，您可以使用用户的`plan`属性来启用基于他们的计划的特性。你可以在图书馆的[布尔的反应页面](https://www.npmjs.com/package/@ff00ff/boolean-react)上读到更多。

我是马丁，布尔的创始人。如果你有任何问题，只要给我一个平。你准备好开始使用特性标志了吗？