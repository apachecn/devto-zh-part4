# 如何在网络上配置纱线

> 原文：<https://dev.to/saul/how-to-configure-yarn-on-netlify-14bl>

Netlify Build 是一个很好的平台，可以运行静态的站点构建，结合 Git 工作流和 Headless CMS 内容更新(通过 Webhooks)。它使得运行基于云的 JavaScript 静态站点生成器变得非常流畅，但也不是没有缺陷。

许多流行的生成器使用 Yarn 作为包管理器，可以通过 Netlify 的 [`netlify.toml`](https://www.netlify.com/docs/netlify-toml-reference/) 配置文件进行配置，比如:-

```
# netlify.toml

[build.environment]
  YARN_VERSION = "1.17.3"
  NODE_ENV = "production" 
```

Enter fullscreen mode Exit fullscreen mode

**注**:指定所需版本的“纱线批准”方法是通过 [`yarn policies set-version`命令](https://yarnpkg.com/lang/en/docs/cli/policies/)实现的。

在本地开发之外的环境中运行构建步骤让我想知道是以`dependencies`还是`devDependencies`的身份安装软件包。当环境变量`NODE_ENV`设置为`"production"`时，Yarn 将*仅*安装`package.json`文件的`dependencies`部分中指定的依赖项，而不安装`devDependencies`中的依赖项。

或许可以在 build 命令的范围内将`NODE_ENV`设置为`"production"`？例如:-

```
{  "scripts":  {  "build":  "NODE_ENV=production ..."  }  } 
```

Enter fullscreen mode Exit fullscreen mode

最干净的解决方案(依我拙见)是将 [`--production`标志](https://yarnpkg.com/lang/en/docs/cli/install/#toc-yarn-install-production-true-false)设为`false`，像这样:-

```
# netlify.toml

[build.environment]
  YARN_VERSION = "1.17.3"
  YARN_FLAGS = "--production=false"
  NODE_ENV = "production" 
```

Enter fullscreen mode Exit fullscreen mode

您可以在 [Netlify 文档](https://www.netlify.com/docs/build-settings/#build-environment-variables)中找到更多关于配置构建环境变量的信息。