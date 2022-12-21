# 用 Svelte 和 Rollup 导入依赖项？

> 原文：<https://dev.to/thehanna/import-dependencies-with-svelte-and-rollup-21aa>

我在尝试苗条身材。我已经按照[上的说明开始了一个项目](https://svelte.dev/blog/the-easiest-way-to-get-started#2_Use_degit)。我做了一些小的定制，并添加了一个库来帮助进行获取请求( [build-url](https://www.npmjs.com/package/build-url) )。

一切都很好！我可以在我的`Home.svelte`文件:
中使用这个语法导入构建 url

```
import buildUrl from '../node_modules/build-url'; 
```

然而，我是从棱角分明的地方走来的；我非常非常习惯于从我的代码中完全省略掉路径`node_modules`；Angular 只是找到了自己需要的东西。我理想的最终状态应该是这样的:

```
import buildUrl from 'build-url'; 
```

有没有我可以用来模拟这种行为的汇总配置？我并不反对使用一个`node_modules`文件夹的路径，但是我不禁觉得我这样做是不对的。如果我用 Rollup 做不到，有没有可能用另一个捆绑器比如 webpack 或者 package？