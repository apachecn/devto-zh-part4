# ReferenceError:未定义 primordials

> 原文：<https://dev.to/only8britt/referenceerror-primordials-is-not-defined-140a>

我已经开始做一个从 2017 年开始就没有真正接触过的项目了。可以想象，由于我所有的节点包都过时了，我收到了大量的警告。

当试图运行`npm install`或`npm update`时，这是我得到的错误之一。

```
const { Math, Object } = primordials;
                         ^

ReferenceError: primordials is not defined 
```

这到底是什么意思？？

在谷歌上搜索了一番后，我发现很多人都遇到了这个错误，因为他们运行的 Gulp 版本与他们当前的 node 版本不兼容。

“好的...酷毙了。但是我没有装 Gulp！:< "
:我

我花了大约 45 分钟-1 个小时试图消除这个错误，但是做的事情是从我的依赖列表中删除`sequelize-cli`。

我是如何意识到`sequelize-cli`是问题的根源的？

我删除了我的`node_modules`文件夹，运行了`npm install`,因为我不知道下一步该做什么，并且正在尝试任何我能想到的方法。

```
Brittanys-MacBook-Pro-4:Gabble_Forum britthome$ npm install
npm WARN deprecated gulp-util@3.0.8: gulp-util is deprecated - replace it, following the guidelines at https://medium.com/gulpjs/gulp-util-ca3b1f9f9ac5
npm WARN deprecated minimatch@2.0.10: Please update to minimatch 3.0.2 or higher to avoid a RegExp DoS issue
npm WARN deprecated natives@1.1.6: This module relies on Node.js's internals and will break at some point. Do not use it, and update to graceful-fs@4.x.
npm WARN deprecated minimatch@0.2.14: Please update to minimatch 3.0.2 or higher to avoid a RegExp DoS issue
npm WARN deprecated graceful-fs@1.2.3: please upgrade to graceful-fs 4 for compatibility with current and future versions of Node.js 
```

哇哦！看那个。你这个鬼鬼祟祟的魔鬼。我真的很困惑，在整个目录中寻找这个依赖项，但是没有找到。这时候我有了一个想法，我列出的依赖项之一必须依赖于 Gulp。

我去了 npm 网站，一个一个地搜索我所有的依赖项。在每个包的页面上，您可以看到它依赖于哪些包。当然，这是我列出的最后一个包，但当我最终找到它时，我发现它确实依赖于 Gulp。

那么，下一步是什么？

因为我很久以前就创建了这个项目，所以我不记得每个依赖项的必要性，所以我正在了解每个依赖项，并决定我是否真的需要它。因为我正在使用`sequelize`，我想我将需要某种 CLI，但是也许这个包中有一个本地 CLI 或者我可以使用的另一个包？可能我连`sequelize`都不会用。这么多决定！

只是想分享一下这个小经验。希望它能帮助一些人。