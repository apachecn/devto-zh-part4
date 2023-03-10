# 专门用 React Hooks 开发了我的第一个聊天应用。太棒了。

> 原文：<https://dev.to/mgranados/built-my-first-chat-app-with-react-hooks-exclusively-it-was-great-14dj>

反应钩是`simple`。无论你想到什么形容词来描述你最喜欢的应用程序状态管理工具(redux，mobx ),肯定不会包括`simple`。

我曾经使用过这些 AppState 工具的几个实现:几年前用 Redux sagas 构建了一个完整的站点。对于来自后端设置的人来说，学习曲线非常复杂。一旦你有了如何实现传奇、动作、还原器和存储的概念，你就可以*轻松地*通过不纯的动作调用 API 并刷新 UI。然后我又和 Baobab 一起尝试了一段时间树枝。在易用性方面有所改进。我的下一个项目相当简单，所以我足够幸运，能够避免任何国家管理。这很有趣，而且有点像`frontend wilderness`中的**新鲜空气**。

几个月前，我决定开始一个副业。React Hooks 是一种很酷的新技术，在阅读了他们的动机帖子和它承诺提供的东西后，我完全投入了进去。它实现了。现在从一个端点获取数据并把它放在 UI 上是非常容易的。

```
import React, {useState} from 'react';

const Login = () => {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [loading, setLoading] = useState(false);
  const [loginError, setLoginError] = useState('');

  let errorLabel;
  if (loginError) {
    errorLabel = <Help isColor="white">{loginError} </Help>;
  }
} 
```

这个代码片段足以存储表单中的电子邮件和密码，将其发送到端点，并在出现错误时放置错误。我对它的简单感到惊讶。我喜欢它。

随着我的代码的某些部分变得越来越复杂，钩子对我来说是坚忍不拔的。为钩子扩展一个额外的令人惊奇的 API，你可以调节调用，为你的调用提供逻辑可能需要的额外变量，最后为你的简单状态设置值。提供的示例:

```
 const [newMessage, setNewMessage] = useState('');
  const [post, setPost] = useState(false);
  useEffect(() => {
    async function postMessage() {
      const response = await postNewMessage(
        newMessage,
        props.match.params.id,
        userSession.token,
      );
      if (response.status === 200) {
        setReloadPage(true);
      } else if (response.status === 422) {
        setErrorJoining('Please write something');
      }
    }
    if (post) {
      postMessage();
    }
    setNewMessage('');
    setPost(false);
  }, [post, props.match.params.id, userSession.token]); 
```

代码仍然清晰可辨，并且通过第二个参数中提供的变量，您承认对它们的任何更改都会再次触发钩子。我几乎忘记了组件生命周期的存在和最小的折衷。

这个网络应用并不复杂，但是到目前为止这种体验还是很棒的。(你可以在这里测试:[https://beta.nicetalks.co/](https://beta.nicetalks.co/))
我深深感谢 React 团队发布钩子。他们再次允许创建一个相当简单明了的网络应用程序。对于您现在可能想要开始的任何项目，我都不会推荐它们。