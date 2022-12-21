# 将 learn-dojo 迁移到 Dojo 6

> 原文：<https://dev.to/odoenet/migrating-learn-dojo-to-dojo-6-2p1o>

Dojo 6 的最新版本带来了一些关于如何使用 Dojo 构建应用程序的重大更新。对构建和定制元素有一些增强，但我认为对开发人员来说最大的新特性之一是使用基于功能的小部件。这并不意味着你不能继续使用基于类的部件，但是使用新的基于函数的部件有一些好处。

以防你不知道， [learn-dojo](https://learn-dojo.com) 是一个静态站点，[与 Dojo](https://learn-dojo.com/building-static-site-with-dojo) 一起构建。因此，随着 Dojo 6 开发的进行，我已经在考虑如何将网站迁移到最新的 Dojo。

## 对 Widgets 的更新

让我们来看看一个基本的 [Header](https://github.com/odoe/learn-dojo/blob/df41818497429706e235c7b39437abb5ed4ee3b5/src/widgets/header/Header.tsx) 小部件，它接受属性来显示标题和到主页的链接。

```
// src/widgets/header/Header.tsx
import WidgetBase from "@dojo/framework/widget-core/WidgetBase";
import { tsx } from "@dojo/framework/widget-core/tsx";
import Link from "@dojo/framework/routing/Link";

import * as css from "./Header.m.css";

export default class Header extends WidgetBase<{ title: string }> {
  protected render() {
    const { title } = this.properties || "My Site";
    return (
      <header classes={[css.root]}>
        <div classes={[css.title]}>
          <Link to="/" isOutlet={false} classes={[css.link]}>
            {title}
          </Link>
        </div>
      </header>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个小部件将一个带有`title`的对象作为其属性，该对象是一个`string`。这不是一个复杂的小部件，现在，当我们把它转换成一个基于功能的小部件时，在渲染方面不会有太大的变化，但是在[标题](https://github.com/odoe/learn-dojo/blob/c480ef742b088dd3bea9a28d686c35a3e551271b/src/widgets/header/Header.tsx)上有一些细微的差别。

```
// converted to Dojo 6
// src/widgets/header/Header.tsx
import { tsx, create } from '@dojo/framework/core/vdom';

import Link from '@dojo/framework/routing/Link';

import * as css from './Header.m.css';

const factory = create().properties<{ title: string }>();

export default factory(({ properties }) => {
  const { title } = properties() || 'My Site';
  return (
    <header classes={[css.root]}>
      <div classes={[css.title]}>
        <Link to="/" isOutlet={false} classes={[css.link]}>
          {title}
        </Link>
      </div>
    </header>
  );
}); 
```

Enter fullscreen mode Exit fullscreen mode

首先，`@dojo/framework`中的文件夹`widget-core`已经被重命名为`core`。这只是对 Dojo 的一个组织上的改变。但是另外一个新的就是这个`create`模块的使用。这个`create`模块可以让你为你的渲染函数创建一个工厂方法。

要创建一个基本的工厂渲染函数，您可以这样做。

```
// return a render factory
const factory = create();

export factory(function MyBasicWidget() {
  return <h2>Everything is awesome!</h2>;
}); 
```

Enter fullscreen mode Exit fullscreen mode

但是`Header`小部件需要一些属性，所以我们可以告诉呈现工厂属性是预期的，并且我们可以键入它们。

```
const factory = create().properties<{ title: string }>(); 
```

Enter fullscreen mode Exit fullscreen mode

现在在工厂方法中，将传递一个`properties()`方法，该方法将提供的属性返回给小部件。

```
export default factory(({ properties }) => {
  const { title } = properties() || 'My Site';
  ...
}); 
```

Enter fullscreen mode Exit fullscreen mode

为什么`properties`是一个函数而不仅仅是一个对象？这与允许使用中间件的基于功能的小部件的一些其他特性有关。这可以确保您不会从属性中获得过时的值。

我们将在未来的博客文章中更详细地介绍新的中间件功能。

这个小部件的其余部分看起来像返回 JSX vdom 的前一个版本。

通常建议您向渲染工厂提供名为的*渲染方法，因为这将帮助您跟踪调试中的错误，但这不是必需的。*有时候你只需要生活在边缘*。*

## 基础中间件

Dojo 的突出特性之一是使用了[块](https://learn-dojo.com/dojo-from-the-blocks),当您使用构建时渲染时，这些块允许您在 node 中运行代码。这对于如何构建 learn-dojo 至关重要，因为块用于解析来自 markdown 的帖子，并为代码块和格式运行各种工具。在基于类的小部件中，这是通过使用元来完成的。

下面是如何使用基于类的小部件呈现博客页面。

```
// src/pages/Blog.tsx
import WidgetBase from '@dojo/framework/widget-core/WidgetBase';
import Block from '@dojo/framework/widget-core/meta/Block';
import { tsx } from '@dojo/framework/widget-core/tsx';

import compileBlogIndex from '../blocks/compile-blog-index.block';

import Post from '../templates/blog-post/BlogPost';

import * as css from './Blog.m.css';

export default class Blog extends WidgetBase<{
  standalone?: boolean;
  path?: string;
}> {
  protected render() {
    const { standalone = false, path } = this.properties;
    // run the block as a meta
    const blogs: any = this.meta(Block).run(compileBlogIndex)({});
    // render blog excerpts or single blog post
    return (
      <div classes={[css.root]}>
        {!standalone
          ? blogs &&
            blogs.map((blog: any) => [
              <Post key={blog.file} path={blog.file} excerpt />,
              <hr key={blog.file} />
            ])
          : undefined}
        {path && path.length && <Post key={path} path={path} />}
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们深入了解一下 Dojo 6 中的 [Blog](https://github.com/odoe/learn-dojo/blob/c480ef742b088dd3bea9a28d686c35a3e551271b/src/pages/Blog.tsx) 模块是如何作为基于函数的小部件出现的。

```
// converted to Dojo 6
// src/pages/Blog.tsx
import { tsx, create } from '@dojo/framework/core/vdom';
import block from '@dojo/framework/core/middleware/block';

import compileBlogIndex from '../blocks/compile-blog-index.block';

import Post from '../templates/blog-post/BlogPost';

import * as css from './Blog.m.css';

const factory = create({ block }).properties<{ standalone?: boolean; path?: string }>();

export default factory(({ middleware: { block }, properties }) => {
  const { standalone = false, path } = properties();
  const blogs: any = block(compileBlogIndex)({});

  return (
    <div classes={[ css.root ]}>
      {!standalone ? (
        blogs &&
        blogs.map((blog: any) => [
          <Post key={blog.file} path={blog.file} excerpt />,
          <hr key={blog.file} />
        ])
      ) : (
        undefined
      )}
      {path && path.length && <Post key={path} path={path} />}
    </div>
  );
}); 
```

Enter fullscreen mode Exit fullscreen mode

要在渲染工厂方法中使用它，将块中间件传递给`create`方法，以便它可用于您的渲染工厂。

传递到 create()方法中的任何东西都可以作为中间件用于呈现工厂方法。

```
const factory = create({ block }).properties<{ standalone?: boolean; path?: string }>();

// render factory
export default factory(({ middleware: { block }, properties }) => {...}); 
```

Enter fullscreen mode Exit fullscreen mode

现在，`block`在传递给 render factory 方法的`middleware`属性上可用。

```
const blogs: any = block(compileBlogIndex)({}); 
```

Enter fullscreen mode Exit fullscreen mode

注意，现在您可以像在基于类的方法中一样，独立于任何`meta`助手运行中间件块。这是我最喜欢的基于功能的新部件的特性之一！

## 可组合的小部件

learn-dojo 站点利用创建包装器小部件的能力来呈现提供给它的任何子部件。这是用在类似于 [`Layout`](https://github.com/odoe/learn-dojo/blob/df41818497429706e235c7b39437abb5ed4ee3b5/src/layouts/Layout.tsx) 的小部件中。

```
// src/layouts/Layout.tsx
export default class Layout extends WidgetBase<SiteMeta> {
  protected render() {
    const { title, description, author, footerLinks } = this.properties;
    return (
      <div classes={[css.root]}>
        <Header title={title} />
        <Hero description={description} />
        {/* render the children */}
        <main classes={[css.section]}>{this.children}</main>
        <SignUp />
        <Footer {...{ author, footerLinks }} />
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

就像更新使`properties`成为一个函数，所以你总是有最新的值，同样的道理对于`children`现在是[布局](https://github.com/odoe/learn-dojo/blob/c480ef742b088dd3bea9a28d686c35a3e551271b/src/layouts/Layout.tsx)中的一个函数。

```
// converted to Dojo 6
// src/layouts/Layout.tsx
const factory = create().properties<SiteMeta>();

export default factory(({ children, properties }) => {
  const { title, description, author, footerLinks } = properties();

  return (
    <div classes={[ css.root ]}>
      <Header title={title} />
      <Hero description={description} />
      {/* render the children */}
      <main classes={[ css.section ]}>{children()}</main>
      <SignUp />
      <Footer {...{ author, footerLinks }} />
    </div>
  );
}); 
```

Enter fullscreen mode Exit fullscreen mode

这是关于在小部件中呈现孩子的唯一变化。

## 总结

Dojo 6 是 Dojo 路线图中的一个重要版本，在为应用程序构建反应式小部件方面提供了一些令人兴奋的新功能。还有很多新特性没有在这篇博文中介绍，我们将在以后讨论。详情见[官方道场 6 博文](https://dojo.io/blog/version-6-dojo)。

基于新的 dojo 文档，我能够在一个上午内迁移 [learn-dojo](https://learn-dojo.com/) 。Dojo 中新的基于函数的小部件模式和中间件的使用给我留下了深刻的印象，我们在这篇文章中仅仅触及了皮毛。

敬请关注更多内容！