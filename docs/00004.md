# LitElement 代码段的 Visual Studio 代码扩展更新和新的可用包

> 原文：<https://dev.to/justinribeiro/visual-studio-code-extension-update-for-litelement-snippets-and-new-pack-available-k1d>

经过一段时间的延迟，我开始着手发布 LitElement 的 v2.0.0，Polymer v2 / v3，以及 Visual Studio 代码的 Web 组件片段。这包括围绕 LitElement 快捷方式的一些重要重组，这些快捷方式是令人痛苦地缺乏的。这现在包括快捷方式，如:

```
l-element // LitElement Web Component definition (alias p-lit-element) l-render-html // LitElement render() with html literal l-render-svg // LitElement render() with svg literal l-firstRendered // LitElement firstRender() l-properties // Lit-Element properties by implementing a static properties getter l-get-styles // Lit-Element styles in a static styles property l-get-styles-super // Lit-Element static styles property with super class l-firstUpdated // Lit-Element firstUpdated() l-shouldUpdate // Lit-Element shouldUpdate() l-requestUpdate // Lit-Element requestUpdate() l-updateComplete // Lit-Element await this.updateComplete; 
```

除了这次更新，我还发布了一个新的扩展包， [LitElement Web Components 扩展包](https://marketplace.visualstudio.com/items?itemName=justinribeiro.litelement-web-components-extension-pack)。这个包包括一些主要的插件，如 [lit-plugin](https://marketplace.visualstudio.com/items?itemName=runem.lit-plugin) 和 my snippets extension，但也包括相当数量的工具插件，如 ESLint 和 Import Cost 等。

退一步说，这是一个轻量级的包，但它有望简化刚刚开始使用 LitElement 的开发人员的许多事情。