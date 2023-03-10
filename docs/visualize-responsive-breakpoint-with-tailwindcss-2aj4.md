# 用 TailwindCSS 可视化响应断点

> 原文：<https://dev.to/suin/visualize-responsive-breakpoint-with-tailwindcss-2aj4>

我在用 TailwindCSS 写响应式模板的时候，感觉不太容易知道哪个断点是活动的。

所以我做了一个简单的组件来可视化哪个断点是活动的。下面的代码片段是组件的实现:

```
<div class="fixed top-0 right-0 m-8 p-3 text-xs font-mono text-white h-6 w-6 rounded-full flex items-center justify-center bg-gray-700 sm:bg-pink-500 md:bg-orange-500 lg:bg-green-500 xl:bg-blue-500">
  <div class="block  sm:hidden md:hidden lg:hidden xl:hidden">al</div>
  <div class="hidden sm:block  md:hidden lg:hidden xl:hidden">sm</div>
  <div class="hidden sm:hidden md:block  lg:hidden xl:hidden">md</div>
  <div class="hidden sm:hidden md:hidden lg:block  xl:hidden">lg</div>
  <div class="hidden sm:hidden md:hidden lg:hidden xl:block">xl</div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

将它复制并粘贴到您的 TailwindCSS 模板中，您将在浏览器的右上角看到断点指示器。

[![](img/35c5446599703d7e6801c6985a7f4e7b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2iAtg80S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fxc318jvx7c9ra3eigj6.gif)

此外，您可以在 CodePen 查看现场演示。

看 [CodePen](https://codepen.io) 上 suin ( [@suin](https://codepen.io/suin) )的笔 [Tailwind CSS 响应活动断点指示器](https://codepen.io/suin/pen/PrKVOa/)。