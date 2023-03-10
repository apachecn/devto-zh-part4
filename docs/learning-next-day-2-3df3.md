# 学习 NextJs -第 2 天

> 原文：<https://dev.to/eperedo/learning-next-day-2-3df3>

现在你可以[在 next 中创建你自己的页面](https://dev.to/eperedo/learning-next-day-1-1pdf)了，你当然需要一种方法在它们之间导航，而不需要直接在你的浏览器中写 url。

为此，下一个框架已经内置了一个名为 **Link** 的组件。
让我们在主页上添加两个链接，一个是根页面，另一个是
**关于**页面。

```
import Link from "next/link";

function Home() {
  return (
    <div>
      <Link href="/about">
        <a>Home</a>
      </Link>
      <Link href="/about">
        <a>About</a>
      </Link>

      <h1>Welcome to my App!</h1>
    </div>
  );
}

export default Home; 
```

请注意，**链接**组件只是一个包装器，为您提供页面之间的客户端转换，这就是为什么我在其中放置了一个**“a”**标签。
如果你在[打开浏览器 http://localhost:3000](http://localhost:3000) 你可以很容易地在它们之间导航。

### 预取

你可以将**预取**道具传递给**链接**组件。如果下次看到这个道具，它会...我们会预取您的页面。

```
import Link from "next/link";

function Home() {
  return (
    <div>
      <Link href="/about">Home</Link>
      <Link prefetch href="/about">
        About
      </Link>

      <h1>Welcome to my App!</h1>
    </div>
  );
} 
```

在上面的代码中，next 将在一个后台进程中下载所有与 about 页面相关的 javascript 代码，从而加快与该页面的交互。这是一个**生产专用的**特性，所以你只能在你的应用部署后测试它。