# react-devto: React 钩子

> 原文：<https://dev.to/bdbch/react-devto-react-hooks-for-dev-to-105>

你好亲爱的开发者社区！

几天前，我向 dev.to 推送了我的 React hooks 库，可以用来获取文章和用户信息。

你可以在这里找到它。

[演示](https://bdbch.github.io/react-devto/)

### 安装

```
npm i @d2k/react-devto --save 
```

### 用法:

```
import {
  useArticles,
  useFollowSuggestions,
  useTags,
  useUser
} from "@d2k/react-devto";

const MyComponent = () => {
  // useArticles(page, tag, username)
  const { articles, loading, error } = useArticles();

  // useFollowSuggestions()
  const { suggestions, loading, error } = useFollowSuggestions();

  // useTags(page)
  const { tags, loading, error } = useTags();

  // useUser(username, id)
  const { user, loading, error } = useUser("bdbch");
}; 
```