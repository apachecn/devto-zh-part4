# npm 命令你可能不知道，但很有用！！！

> 原文：<https://dev.to/0xkoji/npm-commands-you-may-not-know-anj>

### `npm home packageName`

这个命令打开包的主页。

```
$ npm home express 
```

Enter fullscreen mode Exit fullscreen mode

### `npm ls --depth 0`

列出您安装的软件包

```
$ npm ls --depth 0 
```

Enter fullscreen mode Exit fullscreen mode

### `npm prune`

该命令将删除不在`package.json`中的包，因此这将有助于保持项目的整洁。

```
$ npm prune 
```

Enter fullscreen mode Exit fullscreen mode

### `npm outdated`

这个命令检查你安装的软件包的最新版本

```
$ npm outdated

styled-components             4.2.0    4.3.1    4.3.1  project_name
stylelint                    10.0.1   10.1.0   10.1.0  project_name
three                       0.104.0  0.104.0  0.105.2  project_name
ts-loader                     5.4.5    5.4.5    6.0.2  project_name 
```

Enter fullscreen mode Exit fullscreen mode

从 m . lang hard a . k . a .[@ riscie](https://dev.to/riscie)
n
[https://github.com/tjunnone/npm-check-updates](https://github.com/tjunnone/npm-check-updates)那里得到了很棒的建议

我们可以一个字检查过时的包，并用一个相当简单的选项进行更新。

```
$ ncu

$ ncu -u 
```

Enter fullscreen mode Exit fullscreen mode