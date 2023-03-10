# 依赖模拟 Vue 单元测试的秘密武器

> 原文：<https://dev.to/anthonygore/dependency-mocks-a-secret-weapon-for-vue-unit-tests-3af>

如果你的 Vue 单文件组件有依赖关系，当你对组件进行单元测试时，你需要以某种方式处理依赖关系。

一种方法是在测试环境中安装依赖项，但是这可能会使您的测试过于复杂。

在本文中，我将向您展示如何通过替换组件依赖图中的模块文件来模仿 Jest 中的模块文件。

*注:本文最初于 2019/09/16 在 Vue.js 开发者博客上发布[。](https://vuejsdevelopers.com/2019/09/16/mock-dependencies-jest-unit-test?utm_source=devto&utm_medium=article&utm_campaign=jsm)*

## 示例场景

假设我们有一个要测试的单文件组件，名为 *Home.vue* 。这个组件是博客应用程序的一部分，它的主要工作是显示文章标题。

为此，它通过导入 Vuex ORM 模型`Post`并调用`all`方法来检索文章。如果你对 Vuex ORM 不熟悉也没关系，重要的是`Post`模型是这个组件的依赖。

*家。视图*

```
<template>
  <ul>
    <li v-for="post in posts">{{ post.title }}</li>
  </ul>
</template>
<script>
import Post from "@/store/models/Post"
export default {
  computed: {
    posts () {
      Post.all();
    }
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

## 单元测试

现在，我们想为这个组件编写一个单元测试，以确认它能够正确呈现。

这个测试的细节并不重要，但是我们可以这样写:首先，我们使用 Vue 测试工具挂载组件。其次，我们将根据组件呈现标记的快照来检查已安装的组件。

*Home.spec.js*

```
import { shallowMount } from "@vue/test-utils";
import Home from "@/views/Home";

describe("Home.vue", () => {
  it("should render correctly", () => {
    wrapper = shallowMount(Home);
    expect(wrapper).toMatchSnapshot();
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 测试错误

如果我们尝试运行这个测试，我们会得到一个错误:

```
"TypeError: Cannot read property 'store' of undefined" 
```

Enter fullscreen mode Exit fullscreen mode

这个错误的原因是组件中的 Post Vuex ORM 模型同时依赖于 Vuex ORM 和 Vuex，而这两个插件都不存在于测试 Vue 实例中。

```
computed: {
  posts () {
    // expects VuexORM and Vuex plugins to be installed
    Post.all();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 嘲笑救援

您可能想在测试 Vue 实例上安装 VuexORM 和 Vuex。这种方法的问题是错误不会就此停止；接下来，它将抱怨 Vuex 商店尚未创建，然后模型尚未安装到 Vuex ORM 数据库等。突然，你的测试中有 20 行代码，非常复杂。

但是事情是这样的:对于这个单元测试来说，帖子来自 Vuex 商店并不重要。我们在这里需要做的就是满足依赖性，所以这就是为什么我们会转向嘲笑。

## 创造一个模拟

创建 mock 最简单的方法是首先在您想要模仿的文件旁边创建一个目录***mock***，然后在这个新目录中创建 mock 模块。如果你遵循这个食谱，Jest 会自动提取文件。

```
$ mkdir src/store/models/__mocks__
$ touch src/store/models/__mocks__/Post.js 
```

Enter fullscreen mode Exit fullscreen mode

在新文件中，导出一个公共 JS 模块。为了让 mock 工作，您需要存根组件调用的 Post 模型的任何方法。

家里唯一用的方法就是`all`。此方法将检索存储中的所有项目。该方法的输出随后被用于馈送给`v-for`。所以我们需要做的就是让`all`成为一个返回数组的函数， *Home* 组件会很高兴。

*src/store/models/_ _ mocks _ _/post . js*

```
module.exports = {
  all: () => []
}; 
```

Enter fullscreen mode Exit fullscreen mode

## Jest 如何解析依赖关系

我们现在想让 Home 组件使用模拟 Post 模型，而不是“真正的”Post 模型。

在我向您展示如何做到这一点之前，我需要简要解释 Jest 如何像 Webpack 一样，在运行您的测试代码时构建依赖关系图。换句话说，它从您的测试文件开始，然后跟随每个`import`和`require`语句，注意每个需要的模块。

目前，与我们正在讨论的相关的依赖图的一个路径是:

```
Home.spec -> Home -> Post -> Vuex ORM -> Vuex -> ... 
```

Enter fullscreen mode Exit fullscreen mode

这种依赖路径是我们正在经历的错误的根源。

幸运的是，Jest 允许我们用指定的模块替换依赖图中的模块。如果我们使用我们的 *Post* mock，上面的路径将被修改成这样:

```
Home.spec -> Home -> Post (mock) 
```

Enter fullscreen mode Exit fullscreen mode

解决方案的关键是，与真正的 Post 模型不同，mock 没有进一步的依赖，因此如果我们使用它，就不会再出现 TypeError。

## 利用嘲弄

为了使用 mock，我们使用了`jest.mock`方法。这放在文件的顶部，因为它与 import 和 require 语句同时被处理。

第一个参数是您想要模仿的模块，在本例中是`"@/store/models/Post"`。如果您将 mock 放在一个如上所述的 *__mocks__* 目录中，那么这就是工作所需的全部内容。

*Home.spec.js*

```
import { shallowMount } from "@vue/test-utils";
import MyComponent from "@/MyComponent";
jest.mock("@/store/models/Post");

describe("MyComponent.vue", () => {
  it("should render correctly", () => {
    wrapper = shallowMount(MyComponent);
    expect(wrapper).toMatchSnapshot();
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

当您再次运行这个测试时，Jest 将确保依赖图被修改，用您创建的 mock 替换`"@/store/models/Post"`,而不是类型错误，您将得到一个绿色的勾号。

* * *

喜欢这篇文章吗？

通过 *Vue.js 开发者简讯*每周在您的收件箱中获取更多类似的文章。

[点击这里加入！](https://vuejsdevelopers.com/newsletter?utm_source=devto&utm_medium=article)

* * *