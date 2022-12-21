# 使用 Javascript 和 JSON 自动完成国家应用程序

> 原文：<https://dev.to/desoga/auto-complete-country-application-with-javascript-json-4ah2>

> 我学得越多，就越觉得自己有无穷无尽的天赋。
> ——阿达·洛芙莱斯

## 简介

今天我们要制作一个自动完成应用程序，显示世界上不同国家的首都和国家代码。为此，我们将使用一个包含我们需要的所有数据的`json`文件。

这是我们的[项目](https://countrypedia.netlify.com/)的一个工作示例

因此，不会有外部 api，只有一个我创建的更新的`json`文件，其中包含我认为是世界上所有的国家。

如果你发现也许你的国家没有被包括在内，请随意做一个`pull request`因为我会在教程的最后放上 github repo 的链接。

## 要求

*   Html 基础知识。

*   css 的基础知识。

*   JSON 的基础知识

*   javascript 基础知识。

我们只需要三个步骤来完成这个应用程序。

## 第一步

在这一步中，我们将创建应用程序的设计。我们将利用[具体化](https://materializecss.com/) css。是基于材料设计的现代响应式前端框架。

我们还将使用[材质](https://google.github.io/material-design-icons/)图标。

您需要做的就是创建一个`index.html`文件和一个`style.css`文件。