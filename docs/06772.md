# TSLint ->斯拉夫语/CRA/Typescript/Prettier

> 原文：<https://dev.to/info5am/tslint-eslint-cra-typescript-prettier-2h40>

如果您已经阅读了我的前一篇文章，并在 React 应用程序中添加了 TSLint 支持，那么在某个时候，您将不得不迁移到 ESLint，因为 TSLint 将被弃用(我在“使用 Typescript 在 VS 代码中为 React 应用程序设置 TSLint 和更漂亮的 TSLint”中也提到了这一点)。

下面我描述了成功迁移的步骤，并提供了文件配置，使您更容易在带有 Typescript 的 React 应用程序中使用 ESLint。尽情享受吧！

## package.json

你的 *package.json* 至少应该包含以下版本:
`[typescript@3.4.5](mailto:typescript@3.4.5)`
`react-scripts@^3.0.1`
`react@^16.8.6` 

### 脚本

在 package.json 中指定脚本:

`“lint:ts”: “eslint --ext=jsx,ts,tsx src”`

## 删除所有与 TSLint 相关的东西(如果有的话)

`yarn remove @typescript-eslint/eslint-plugin-tslint tslint tslint-config-prettier tslint-react`

## 安装斯洛文尼亚语

⚠️·埃斯林特公司将推出 Create-React-App，所以你可以跳过这一步。

可以用 yarn 或者 npm:
`npm install eslint --save-dev`安装 ESLint

`yarn add eslint --dev`

## 设置一个配置文件(。eslintrc)

然后，您应该设置一个配置文件:
`$ ./node_modules/.bin/eslint --init`

您必须通过提示，选择将进入自动生成的 **.eslintrc.js** 文件的内容。

如需更多详细信息，请查看此链接:[配置 ESLint](https://eslint.org/docs/user-guide/configuring?source=post_page-----4befb4918ba8----------------------)

当然，如果你想得到 typescript，react 和更漂亮的支持，你仍然需要定制这个文件。

## .eslintrc.js

所以，这几乎是主配置文件。它包含了关于你正在使用的林挺插件的信息。您也可以从这里调整推荐的规则。