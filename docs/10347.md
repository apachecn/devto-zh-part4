# react@16.8.6 或 react @ Laravel 中的下一个设置。

> 原文：<https://dev.to/mustaktalukder/react-16-8-6-or-react-next-setup-in-laravel-10ja>

## 如何在 Laravel 中使用 [react@16.8.6](mailto:react@16.8.6) 或 react@next？

步骤 1:用 composer 命令创建您的 laravel 项目

## == >作曲家创作-项目-首选-dist laravel/laravel 博客

第二步:在你的 laravel 中安装 react。

## == > php 匠预置 react

步骤 3:安装所有的依赖项。

## == > npm 安装或纱线安装

第四步:检查，是工作吗？

## == > npm 运行开发或纱线运行开发

如果一切正常的话？下一步，否则重新检查这些命令。

## 第五步:转到你项目中的“package.json”文件。

第 6 步:编辑这两行 package.json 文件。设定您想要的版本。

```
 "react": "^16.8.6",
    "react-dom": "^16.8.6", 
```

第 7 步:然后再次在所有的依赖关系

## == > npm 安装或纱线安装

所有的指挥部分都结束了。

第八步:进入资源/视图/welcome.blade.php

## set == >在 body 标签下板条箱一个 div 并设置 id="example "

```
 <body>

    <div id="example"></div>

</body> 
```

一切都好。

步骤 9:运行 laravel 服务器并构建 react。

## == >纱线运行开发或 npm 运行开发

## == > php 匠服侍

刷新您的浏览器

会有用的。

谢谢你。