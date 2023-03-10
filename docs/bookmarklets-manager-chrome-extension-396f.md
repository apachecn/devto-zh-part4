# 🔖书签管理器 Chrome 扩展！

> 原文：<https://dev.to/ahmedmusallam/bookmarklets-manager-chrome-extension-396f>

您需要安装的所有信息都在这里:

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [艾哈迈德-穆萨拉姆](https://github.com/ahmed-musallam)/[chrome-bookmarklet-manager](https://github.com/ahmed-musallam/chrome-bookmarklet-manager)

### 一个管理书签的 chrome 扩展！

<article class="markdown-body entry-content p-5" itemprop="text">

# <g-emoji class="g-emoji" alias="bookmark" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f516.png">🔖</g-emoji> Chrome 书签管理器

用[摩纳哥编辑器](https://microsoft.github.io/monaco-editor/)编辑[书签](https://en.wikipedia.org/wiki/Bookmarklet)的 chrome 扩展。

[![bookmarklet manager demo](img/6af8006582dff88ff3a75ad0e835c106.png)](https://raw.githubusercontent.com/ahmed-musallam/chrome-bookmarklet-manager/master/docs/bookmarklets-manager.gif)

> bookmarklet 是存储在 web 浏览器中的书签，它包含 JavaScript 命令，可为浏览器添加新功能。

## 安装

> 等待对 chrome 商店的审查

*   转到`chrome://extensions/`
*   启用`Developer mode`滑块
*   从(releases)下载最新的`.ext`([https://github . com/Ahmed-musallam/chrome-bookmarklet-manager/releases](https://github.com/ahmed-musallam/chrome-bookmarklet-manager/releases))
*   将`.ext`文件拖到 chrome 上进行安装

## 发展

*   克隆此回购
*   转到`chrome://extensions/`
*   启用`Developer mode`滑块
*   点击`Load unpacked`
*   从克隆的存储库中选择`ext`文件夹
*   运行`npm run dev`:将观察源代码变化并构建 Js 包，并将其放入`ext`文件夹中。

</article>

[View on GitHub](https://github.com/ahmed-musallam/chrome-bookmarklet-manager)

这个项目已经酝酿了很长时间。我想要一个 bookmarklet 管理器扩展，它不会在页面上运行任何代码，除非用户实际触发了所述代码。这个扩展没有运行[内容脚本](https://developer.chrome.com/extensions/content_scripts),这意味着扩展本身不会加载任何代码到你访问的页面上，也不会关心！这个扩展所做的，就是给你一个在一个好的编辑器中编辑以`javascript:`开始的书签的方法。哦，我忘了说它使用了[摩纳哥编辑](https://microsoft.github.io/monaco-editor/)？VSCode 背后的编辑器！

试试吧，让我知道你的想法！我将感谢任何输入/拉请求/星你可以给！

PS。这个扩展是用 Vue 构建的，我不是 Vue 专家。还在学习:)