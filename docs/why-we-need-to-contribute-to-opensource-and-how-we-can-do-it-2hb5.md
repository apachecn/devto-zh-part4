# 为什么我们需要为开源做贡献，以及我们如何去做

> 原文：<https://dev.to/askjkrishna/why-we-need-to-contribute-to-opensource-and-how-we-can-do-it-2hb5>

<small>图像由 Unsplash 生成</small>

### 为什么我们需要构建或发布一个？

Javascript 生态系统是巨大的。当我们需要解决我们的问题时，我们经常搜索并找到解决方法。

但是如果没有，最好与大家分享我们提出的解决方案。它使那些最初帮助我们的人的生活变得容易。

### 什么激励我为开源做贡献

*   IDE 的 [VSCode](https://github.com/microsoft/vscode) ， [Atom](https://github.com/atom/atom)
*   现有的流行库或框架- [Babel](https://github.com/babel/babel) 、 [React](https://github.com/facebook/react) 、 [Angular](https://github.com/angular/angular) 、 [Vue](https://github.com/vuejs/vue) 、 [Stencil](https://github.com/ionic-team/stencil) 、 [Preact](https://github.com/preactjs/preact) 、 [Redux](https://github.com/reduxjs/redux/blob/master/LICENSE.md) 、 [Vuex](https://github.com/vuejs/vuex) 、 [NextJS](https://github.com/zeit/next.js/) 、 [NuxtJS](https://github.com/nuxt/nuxt.js/blob/dev/LICENSE) 、 [Svelte](https://github.com/sveltejs/svelte/blob/master/LICENSE)
*   捆扎机- [网袋](https://github.com/webpack/webpack/blob/master/LICENSE)、[卷装](https://github.com/rollup/rollup/blob/master/LICENSE.md)、[包裹](https://github.com/parcel-bundler/parcel/blob/master/LICENSE)
*   Css-in-JS - [魅力](https://github.com/threepointone/glamor/blob/master/LICENSE)、[风格化-组件](https://github.com/styled-components/styled-components/blob/master/LICENSE)、[情感](https://github.com/emotion-js/emotion/blob/master/LICENSE)
*   设计系统和工具- [Antd](https://github.com/ant-design/ant-design/blob/master/LICENSE) ，[故事书](https://github.com/storybookjs/storybook/)，[风格化系统](https://github.com/styled-system/styled-system)， [Styleguidist](https://github.com/styleguidist/react-styleguidist)
*   后端框架- [NestJS](https://github.com/nestjs/nest/blob/master/LICENSE) ，[无服务器](https://github.com/serverless/serverless/blob/master/LICENSE.txt)
*   CI/CD 工具- [Travis](https://travis-ci.org/)
*   测试工具- [Jest](https://github.com/facebook/jest) ，[测试库](https://github.com/testing-library/react-testing-library)
*   实用工具- [莱娜](https://github.com/lerna/lerna)，[维尔达奇奥](https://github.com/verdaccio/verdaccio)

由个人构建的个人项目和工具不胜枚举。

是的，我同意其中一些是由大公司支持的，但是我们不是在靠它们谋生吗？

* * *

### 提问时间！！

很多项目一开始真的很容易拿到巨额资金吗？

当他们在构建和开源一切的时候，他们是如何管理他们的财务的？

* * *

### 鼓舞人心的故事或思想

*   有些人放弃了他们的日常工作，因为害怕他们不能尽快回复或解决一个大问题。因为世界上许多项目都依赖于它们。

*   如果你想要一个补丁或者一个新功能。在提出请求(问题)的同时，尝试修复/构建它。维护人员总是乐于鼓励和帮助愿意做出贡献的人。

如果我没猜错的话，第一封来自于[亨利·朱](https://twitter.com/left_pad)，第二封来自于[萨拉·维利亚](https://twitter.com/nikkitaftw)T5】

* * *

### 我们可以为生态系统做贡献的方式

我们可以通过几种方式来支持生态系统。

*   通过构建和开源软件包。
*   捐赠和支持存储库和维护者
*   修复现有问题(至少在我们日常开发中使用的存储库上)。
*   帮助撰写文档和纠正错别字。
*   审查 pull 请求并指导新手开发开源软件。[编码蔻驰](https://codingcoach.io/)
*   帮助建立项目的基础设施/架构。
*   协调社区，更像是整合问题和 PRs 以及其他*琐事*相关的事情。

* * *

> [Maintainers Anonymus](https://maintainersanonymous.com/) 是由 [Henry Zhu](https://twitter.com/left_pad) 主持的播客，了解更多关于 Maintainers 的信息。

* * *

### 如何打造一个？

在尝试了不同类型的设置之后，我个人更喜欢使用 typescript。因为它易于配置。理想情况下，我们可以将此分为两个阶段。

*   建造图书馆本身。
*   维护存储库。

让我们来看看
建筑设置的文件夹结构

```
Readme.md
src -
    | index.ts
tsconfig.esm.json
tsconfig.cjs.json
__tests__
package.json 
```

*   `package.json`是为了提及我们项目的`dependencies`、`devDependencies`和`entry`。
*   `tsconfig.esm.json`用于编译目标最新环境的类型脚本代码
*   `tsconfig.cjs.json`是针对旧环境的。
*   我们的代码依赖于此。

### package.json

这在包中起着非常关键的作用，所以让我们更深入地看看属性。

```
{  "name":  "",  "version":  "",  "description":  "",  "author":  "",  "homepage":  "",  "license":  "",  "main":  "",  "module":  "",  "types":  "",  "repository":  "",  "bugs":  {  "url":  ""  },  "scripts":  {},  "dependencies":  {},  "devDependencies":  {}  } 
```

很少有事情是不言自明的，但让我们看看其他的。

*   *版本*，起着非常重要的作用。遵循版本控制的最好方法是改编[永远](https://semver.org/)
*   *主*，指出要包的入口。通常是指向 cjs 捆绑包`index.js`文件。
*   *模块*指向文件的`esm`包。 [Pika](https://www.pika.dev/) 是一个强大的包管理器，它利用了这一点。
*   *类型*指向`typings`文件。当我们使用软件包时，类型文件有助于添加类型。
*   *dependencies* 处理我们在建筑中使用的所有包。
*   devDependicies 处理我们在开发时使用的所有包。这些都没有捆绑到产品包中。 [Bundlephobia](https://bundlephobia.com/) 有助于了解单个包装最终占据的空间。
*   当我们希望我们的存储库依赖于另一个存储库时。主要用于 UI 相关库的开发，因为这些框架很少依赖于其他框架。

* * *

### 变更日志管理

Changelog 在跟踪软件包中的所有变更和发布中起着重要的作用。
[Lerna Changelog](https://github.com/lerna/lerna-changelog) 让管理一个变得容易。

* * *

### 常见的陷阱要看出来！！

*   版本控制起着非常重要的作用，因为波浪号(~)只安装补丁，(^) cap 安装最新的补丁和小的修正。

如果用户使用的是*波浪号*，我们会捆绑所有修复，并进行版本升级和发布。当前用户将不会获得修复，因为(~)只会获取补丁。

*   *缺少依赖*，当我们在开发中使用一个没有在`package.json`中列出的包时。

如果同一个包被`devDependencies`使用，执行不会像在本地那样抛出任何错误。这是因为它是从`node_modules`中解析出来的，因为它是依赖关系的唯一真实来源。

这可以通过使用 [Pika CDN](https://www.pika.dev/cdn) 根据 PR 测试包并独立安装和验证来消除。

以下是我对开源以及如何开始成为其中一员的一些看法。