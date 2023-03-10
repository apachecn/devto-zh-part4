# 基于 CakePHP 3.x 的多角色仪表板后端模板，又名 Cristeena

> 原文：<https://dev.to/msamgan/cakephp-3-x-based-multi-role-dashboard-back-end-template-aka-cristeena-2gn4>

克里斯汀会让你从启动新项目的麻烦中解脱出来。它提供了在 CakePHP 3.x 中启动一个项目所需的所有特性。

## 角色

这是一个用 CakePHP 3.x 创建后端应用程序的框架

1.  主任(超级管理员)
2.  管理
3.  用户

## 层次结构

以上所有角色都有单独的仪表板。系统的用户部分有集成自己选择的前端的空间。

在开发这一功能时，还会考虑到层次结构，例如主管可以对管理员和用户执行操作，但管理员只能对用户执行操作。

## 速度

我使用 RequireJS 来加载系统中的所有 JS，这减少了页面加载时的 HTTP 请求，使系统非常快。此外，系统使用 CakePHP ORM 进行数据库交互，速度越来越快。

## 支持

所有开放的问题都是受欢迎的，并将得到认真对待。为了所有人的共同利益，我将非常高兴在这方面有合作者。

## 文档化。

你可以在这里找到这个项目的详细文档。

## 去吧

这里是[存储库链接](https://github.com/samgan-khan/cristeena)

快乐编码。

小船