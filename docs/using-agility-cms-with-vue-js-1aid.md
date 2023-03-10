# 通过 Vue.js 使用 Agility CMS

> 原文：<https://dev.to/joelvarty/using-agility-cms-with-vue-js-1aid>

我在 Agility CMS 的同事们就 React 和 Vue 哪个更好展开了激烈的竞争。因为 React 有更多的追随者，我们从 Agility CMS 的“create-react-app”开始- [见这里的使用指南](https://help.agilitycms.com/hc/en-us/articles/360031121692-Using-Agility-CMS-with-Create-React-App-CRA-)，作者是了不起的 James Vidler。

对于我们这些真正热爱 Vue.js 的人来说，我创建了一个初始项目模板，你现在就可以从 GitHub 克隆它。

### 步骤 1 -创建一个免费的 Agility CMS 账户

*   敏捷 CMS 有一个免费层- [现在就注册](https://account.agilitycms.com/sign-up)
*   选择博客模板。

### 步骤 2 -克隆存储库

*   GitHub 上有您需要的所有入门代码
*   现在就克隆它:[https://github.com/joelvarty/agility-vue-app](https://github.com/joelvarty/agility-vue-app)

```
git clone https://github.com/joelvarty/agility-vue-app 
```

Enter fullscreen mode Exit fullscreen mode

### 第三步——本地运行

*   如果你对 Vue.js 非常熟悉，你应该能够让它在本地运行。
*   如果你有困难，或者你不熟悉 Vue.js，[从这里开始](https://vuejs.org/v2/guide/)

```
npm install
npm run serve 
```

Enter fullscreen mode Exit fullscreen mode

### 步骤 4 -使用 React 指南

*   在 *React* 中有一个引导你完成所有事情的启动指南，但它实际上可以很好地翻译成 Vue.js。
*   在这里阅读:[https://help . Agility CMS . com/HC/en-us/articles/360031121692-Using-Agility-CMS-with-Create-React-App-CRA-](https://help.agilitycms.com/hc/en-us/articles/360031121692-Using-Agility-CMS-with-Create-React-App-CRA-)
*   一旦您插入了 GUID 和 API 键，您应该能够在 Agility Content Manager 中进行更改，并看到它们反映在您的本地项目中。

### 第五步——奖金！-将其部署到 Netlify

*   我们喜欢 Netlify——它是一个伟大的工具，也有一个免费层(智能！).
*   同样，我们有一个将您的站点部署到 Netlify 的 React 指南
*   看这里:[https://help.agilitycms.com/hc/en-us/articles/360032203552](https://help.agilitycms.com/hc/en-us/articles/360032203552)
*   注意，Vue.js 默认构建到 *dist* 文件夹，而 React.js 使用 *build* 文件夹

```
npm run build
npm install -g netlify-cli
netlify login
netlify deploy --dir=dist --open 
```

Enter fullscreen mode Exit fullscreen mode

* * *

恭喜你。你已经朝着使用一个惊人的无头 CMS 和一个非常强大的框架迈出了第一步。

我真的很想在下面的评论中听到你的反馈。