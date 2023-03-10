# 自动设置更漂亮和更沙哑，更漂亮的配置

> 原文：<https://dev.to/sosukesuzuki/setting-up-prettier-and-husky-automatically-with-prettier-configure-e57>

[https://github.com/sosukesuzuki/prettier-configure](https://github.com/sosukesuzuki/prettier-configure)

(请放个星！⭐)

我开发了更漂亮的配置。它可以自动设置更漂亮和更沙哑。

## 用法

**它假设 package.json 已经存在于您的当前目录中。**

您不必安装更漂亮配置。您可以通过 npx 运行它，如下所示:

```
npx prettier-configure 
```

如果你运行它，漂亮配置执行以下三个步骤:

### 1。通过 yarn(或 npm)将 beauty 和 Husky 安装到您的 devDependencies 中。

prettire-configure 为您的 devDependencies 安装更漂亮、更健壮的程序。你可以通过`manager`参数选项选择你喜欢的包管理器。

```
npx prettier-configure --manager=npm 
```

### 2。在 package.json 中添加 format with appellister 和 config for Husky 的 npm 脚本。

漂亮-配置插入“脚本”和“哈士奇”到你的包. json.

```
{  "scripts":  {  "format":  "prettier --write "**/*.{js,jsx,ts,tsx,md,json,yaml,html}""  },  "husky":  {  "hooks":  {  "pre-commit":  "pretty-quick --staged"  }  }  } 
```

(注意:如果您的 package.json 已经有“脚本”,它将被重写。)

### 3。在当前目录下创建`.prettierrc.yaml`和`.prettierignore`。

用我喜欢的默认设置创建文件。如果其他人想使用此工具，我将启用您可以更改默认设置。

`.prettierrc.yaml`

```
trailingComma: 'all'
singleQuote: true
tabWidth: 4 
```

`.prettierignore`

```
/node_modules
package.json 
```