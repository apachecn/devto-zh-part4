# 使用 Jest 预置简化 Gatsby 单元测试

> 原文：<https://dev.to/keplersj/simplifying-gatsby-unit-testing-using-a-jest-preset-5g3c>

最近，我一直在扩展我的网站和博客的代码库，以便有一个更强大的网站。其中一部分当然包括使用 Jest 对源代码进行单元测试。这个代码库的测试套件主要由 [Jest Snapshot 测试](https://jestjs.io/docs/en/snapshot-testing)组成，以确保组件和它们构建的页面按预期运行和呈现。像任何代码库一样，创建像这样健壮的单元测试有许多优点；这个代码库的主要功能是使用[依赖机器人](https://dependabot.com)进行自动依赖更新，以及使用 [Stryker](https://stryker-mutator.io) 进行变异测试(以确保测试有效运行)。

我以前在很多项目中使用过 Jest，并构建了一些 Jest 插件来改善体验。我构建了 Jest runners 来运行 linters 和单元测试( [`jest-runner-prettier`](https://keplersj.com/projects/jest-runner-prettier) 、 [`jest-runner-stylelint`](https://keplersj.com/projects/jest-runner-stylelint) 和 [`jest-runner-tslint`](https://keplersj.com/projects/jest-runner-tslint) )，我还构建了 transformer 模块来使使用`webpack`项目更容易 [`jest-raw-loader`](https://keplersj.com/projects/jest-raw-loader) 。所以，当我看到我的盖茨比代码库还有改进的空间时，我就知道该怎么做了。

# 利用 Jest 的预置功能减少配置样板文件

关于单元测试的 Gatsby 文档非常棒，非常有帮助(就像 Gatsby 文档的其他部分一样)。)它带你通过单元测试你的站点代码的绳索，让你几乎没有摩擦地快速启动并运行。然而，它有一个缺陷:它要求您创建的配置不小心创建了许多样板文件。幸运的是，Jest 内置了一个功能，可以让[从预设的](https://jestjs.io/docs/en/configuration#preset-string)中抽象出样板文件。

Jest 预置可以在 Jest 文档中找到，作为在已知环境中使测试更容易的推荐方法。他们习惯于[测试 React 本地](https://jestjs.io/docs/en/tutorial-react-native)项目，[用木偶师](https://jestjs.io/docs/en/puppeteer)运行测试，并使测试 [MongoDB](https://jestjs.io/docs/en/mongodb) 和 [DynamoDB](https://jestjs.io/docs/en/dynamodb) 更容易。考虑到这一点，让我们抽象出配置 Jest 来运行 Gatsby 代码所需的样板文件，让我们的生活稍微轻松一点。

我已经创建了[一个模块](https://keplersj.com/projects/jest-preset-gatsby)，它从 Gatsby 文档中导出[默认的 Jest 配置作为 Jest 预置。通过使用它，我们的 Jest 配置来自于:](https://www.gatsbyjs.org/docs/unit-testing/#2-creating-a-configuration-file-for-jest) 

```
module.exports = {
  transform: {
    "^.+\\.jsx?$": "<rootDir>/.jest/gatsby-preprocess.js"
  },
  testRegex: "(/__tests__/.*|(\\.|/)(test|spec))\\.(jsx?)$",
  moduleNameMapper: {
    ".+\\.(css|styl|less|sass|scss)$": "identity-obj-proxy",
    ".+\\.(jpg|jpeg|png|gif|eot|otf|webp|svg|ttf|woff|woff2|mp4|webm|wav|mp3|m4a|aac|oga)$":
      "jest-raw-loader"
  },
  moduleFileExtensions: ["js", "jsx", "json", "node"],
  testPathIgnorePatterns: ["node_modules", ".cache"],
  transformIgnorePatterns: ["node_modules/(?!(gatsby)/)"],
  globals: { __PATH_PREFIX__: "" },
  testURL: "http://localhost",
  setupFiles: ["<rootDir>/.jest/loadershim.js"]
}; 
```

对此:

```
module.exports = { preset: "jest-preset-gatsby" }; 
```

## 也是打字稿！

抽象这个模板还有一个额外的好处，那就是让我们这些 TypeScript 用户的生活变得更加轻松。将 Jest 配置为在 Gatsby+TypeScript 项目中运行变得像在 Jest 配置中设置以下内容一样简单:

```
module.exports = { preset: "jest-preset-gatsby/typescript" }; 
```

# 从头安装

有兴趣测试您的 Gatsby 项目，还没有配置它？现在是开始的好时机！

## JavaScript 项目

使用 npm:
安装`jest`、`react-test-renderer`和`jest-preset-gatsby`

```
npm install --save-dev jest react-test-renderer jest-preset-gatsby 
```

或者纱线:

```
yarn add --dev jest react-test-renderer jest-preset-gatsby 
```

并将 Jest 配置为使用您的`package.json` :
中的预设

```
{  "jest":  {  "preset":  "jest-preset-gatsby"  }  } 
```

或者`jest.config.js` :

```
module.exports = { preset: "jest-preset-gatsby" }; 
```

就这样，你准备好写你的测试了！请参见[关于编写第一个测试](https://www.gatsbyjs.org/docs/unit-testing/#writing-tests)的 Gatsby 文档。

## 打印项目

使用 npm 安装`jest`、`react-test-renderer`、`jest-preset-gatsby`以及相关的类型定义:

```
npm install --save-dev jest react-test-renderer jest-preset-gatsby @types/jest @types/react-test-renderer 
```

或者纱线:

```
yarn add --dev jest react-test-renderer jest-preset-gatsby @types/jest @types/react-test-renderer 
```

并将 Jest 配置为使用您的`package.json` :
中的预设

```
{  "jest":  {  "preset":  "jest-preset-gatsby/typescript"  }  } 
```

或者`jest.config.js` :

```
module.exports = { preset: "jest-preset-gatsby/typescript" }; 
```

就这样，你准备好写你的测试了！请参见[关于编写第一个测试](https://www.gatsbyjs.org/docs/unit-testing/#writing-tests)的 Gatsby 文档。

# 告诫

虽然这确实让*为盖茨比配置* Jest 变得更容易，但它也有一个警告。这个项目没有为`gatsby`模块提供模拟。请参见[Gatsby 关于嘲讽`gatsby`](https://www.gatsbyjs.org/docs/unit-testing/#3-useful-mocks-to-complete-your-testing-environment) 的文档以便有效地测试你的代码。