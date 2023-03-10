# 使用 AdonisJs - 1 的 TDD 课程。让我们构建一个 reddit 克隆

> 原文：<https://dev.to/michi/tdd-course-with-adonisjs-1-let-s-build-a-simple-forum-199>

> 最初发布于[michaelzanggl.com](https://michaelzanggl.com/articles/tdd-with-adonisjs-1)。订阅[我的简讯](https://michaelzanggl.com/)永远不要错过新内容。

AdonisJs 非常适合测试驱动开发。让我们使用 TDD 为 reddit 的简单版本构建一个 API。它将由子论坛、主题、评论和用户组成。你可以在这里找到它的 GitHub:[https://GitHub . com/MZanggl/TDD-adonisjs/commit/b 2582 b 286 E4 da 0166 f 30 a 8d 6 a 8 eee 7 C3 AE B1 c8 bb](https://github.com/MZanggl/tdd-adonisjs/commit/b2582b286e4da0166f30a8d6a8eee7c3aeb1c8bb)。

事不宜迟，让我们开始建立项目吧！

# 安装 CLI 和框架

```
npm i -g @adonisjs/cli 
```

Enter fullscreen mode Exit fullscreen mode

在本课程中，我们只想关注 adonis 部分，而不是前端，所以让我们使用“仅 api”标志来创建项目。

```
adonis new forum --api-only
cd forum 
```

Enter fullscreen mode Exit fullscreen mode

# 安装测试库

Adonis 自带测试库，我们用
安装吧

```
adonis install @adonisjs/vow 
```

Enter fullscreen mode Exit fullscreen mode

我们必须在 aceProviders 数组中的“start/app.js”下添加 vowProvider 来注册 adonis 测试命令。

```
const aceProviders = [
    '@adonisjs/vow/providers/VowProvider',
] 
```

Enter fullscreen mode Exit fullscreen mode

* * *

vow 的安装附带了一个示例测试，运行`adonis test`、`npm test`或简单的`npm t`来运行它。

为了确保一切正常，使用`adonis serve --dev`运行项目并在浏览器中找到 url！

* * *

这就是全部内容，在下一篇博文中，我们将创建我们的第一个测试！