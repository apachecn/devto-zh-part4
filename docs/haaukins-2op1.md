# 豪金斯·🇬🇧

> 原文：<https://dev.to/mrturkmen/haaukins-2op1>

# Haaukins 💻⏰

这是一个旨在向感兴趣的人提供网络安全教育的项目，主要想法是通过浏览器访问虚拟化环境，并通过 Docker 容器或虚拟机启用所有易受攻击的系统。

为了深入了解如何使用它，我们会遇到以下场景:

想象一下这样一种情况，你有自己的博客，它被部署到你自己的 wordpress 实例中。让我们假设一旦你偶然忘记更新 wordpress 的最新安全漏洞，那么这意味着你的网站容易受到攻击者的攻击。为了在 Haaukins 环境中模拟这种情况，我们使用 Docker 容器并在 Docker 上安装易受攻击的应用程序(wordpress ),并设置一个环境，该环境包含易受攻击系统的所有安全相关工具。

通过这种方式，用户将有机会在完全无害的环境中使用提供的 Kali 实例通过 web 浏览器测试真实世界的场景。此外，在 Haaukins 环境中，没有机会访问虚拟化网络之外的内容。

Haaukins 大量使用 Go 编程语言，但是 Haaukins 环境中存在的挑战可以用你知道的任何编程语言来准备。由于它们被包装在 Docker 映像中，因此可以使用任何编程语言来实现目标。

## 主要成分‼️

*   Docker :大量用于制造存在于 Haaukins 环境中的挑战。打包在 Docker 图像中的任何 CTF(捕获旗帜)挑战都可以在 Haaukins 环境中使用。

*   **Virtualbox** :用于挑战和通过浏览器访问平台。当平台通过浏览器访问时，任何与被访问实例在同一个网络中的挑战都可以被看到和攻击。

*   GoLang :管理一切，从分配团队到创建容器，再到为用户运行虚拟机。(拥有 monolith 开发方法，致力于转化为微服务)

*   VueJS :用于 Haaukins 的前端，它有 web 组件，用于通过 Haaukins Web 管理环境生成的每个事件。

## ⚠️哈尤金斯的其他关联回购

Haaukins 目前**还不是**完全面向微服务的应用或平台，但是随着最近的变化，我们正在慢慢转向具有可插拔服务的微服务架构。

*   [Haaukins 商店](https://github.com/aau-network-security/haaukins-store)
*   [Haaukins 网络客户端](https://github.com/aau-network-security/haaukins-webclient)
*   [哈金斯挑战 API](#WIP) **WIP**
*   [哈金斯虚拟](#WIP) **WIP**

## 更多信息ℹ️

更多关于项目的信息和所有相关文件可从这里获得:[https://github.com/aau-network-security/haaukins](https://github.com/aau-network-security/haaukins)

所有与项目相关的文件都在这里:
[https://docs.haaukins.com](https://docs.haaukins.com)

## 联系✆

任何疑问、问题、建议、想法或者只是聊天，你都可以通过发送电子邮件到 hi@mrturkmen.com 或者通过我的社交账户联系我。也不要犹豫通过 Github 联系我😉