# 新节点包

> 原文：<https://dev.to/revelmind/new-node-package-2ond>

## 大家好！

我的名字是扎克，我是一名开发人员，从一开始就一直致力于这个包。(我创造的！)

它是一大堆代码“片段”的来源，可以随时随地免费使用。`Snippet`应该尽可能众包，所以在我们的 Github 贡献你的代码片段:
[Github 链接](https://github.com/opensource-matrix/snippet)

它有一个易于使用的 API，允许即时访问任何代码片段。

## 用法

有几个函数打包了一些对 Snippet 有用的方法。

```
/* Load up the Node module */
const snippet = require('@matrixoc/snippet'); 
```

### 获取所有片段

```
snippet.getSnippets(); 
```

### 获取某种语言的所有片段

```
snippet.getSnippetsByLanguage('javascript'); 
```

### 按名称获取片段

```
snippet.getSnippetByName('hello_world_js'); 
```

### 检查片段是否存在

```
snippet.checkName('hello_world_js'); 
```

### 检查一种语言是否有任何片段

```
snippet.checkLanguage('javascript'); 
```

## 安装

你可以从 NPM、Yarn 或 RubyGems 下载。

```
npm i @matrixoc/snippet
yarn add @matrixoc/snippet
gem install snippetpkg 
```