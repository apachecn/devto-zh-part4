# 设置 Jest+React-Testing-库

> 原文：<https://dev.to/aromanarguello/getting-started-with-jest-react-testing-library-4nga>

在过去的几个月里，我非常高兴能够在 React + Friends 环境中使用我最喜欢的库 react-testing-library。这个图书馆是一件艺术品。

没有一个库比它更直观、更容易设置、对初学者更友好。在某些情况下，这甚至是让开发人员进入新项目的好方法。通过这篇指南，我的目的是分享我在为不同项目配置工具的过程中学到的东西，以及我个人采用的一些最佳实践。我们将逐步介绍如何使用 create-react-app 设置您的环境，并从头开始设置 react 项目(jest*、web-pack、babel 等)。本指南不仅仅是一个如何测试的指南，而是一个如何在 React 应用程序中设置测试环境的逐步演练。对于学习如何编写单元测试，有很多比我更有知识的人提供的指导来讨论这个话题。然而，我要说的是，对我来说，变得更擅长测试的最好方法是实际编写测试。我看到的没有写过测试的新老开发人员最大的困难之一，就是了解 Jest 的角色和 react-testing 库的角色之间的区别。以我自己的经验，学习这种区别的方法是通过重复，陷入困境，做研究，反复。

渴望听到来自社区的反馈！

## 指标:

*   我学到的目录文件结构和惯例
*   Jest + RTL 和 create-react-app 入门
*   从零开始使用 Jest + RTL
*   使用 Typescript 设置
*   例子
*   帮助您开始了解 RTL 的精选资源

## 文件结构:

我写的关于如何构建我的 React 应用程序(不使用钩子)的文章:
[https://blog . use journal . com/How-I-structure-my-React-Apps-86e 897054593](https://blog.usejournal.com/how-i-structure-my-react-apps-86e897054593)

最佳实践和惯例已经存在很长时间了，甚至在 react 出现之前，创建一个文件夹`__ test __`并把你的测试文件放在这个文件夹中。我做事情有点不同，这绝对只是个人喜好。当我继续使用下面的系统时，它有点卡住了，我的团队和我自己都非常喜欢它(我想！).

## 我的项目中典型的文件结构:

```
- node_modules
- public
- src
  - components
    - MyComponent
      - MyComponent.jsx
      - MyComponent.styles.js
      - MyComponent.test.js      // here is what I do different
    - index.js            // source of truth for component export
  - utils
    - helpers.js
  - pages
  - App.jsx
  - App.test.jsx
  - App.styles.js
  - index.js 
```

Enter fullscreen mode Exit fullscreen mode

正如我在上面的评论中指出的。这是我个人最大的一次与大众惯例的背离。在我看来，在组件驱动开发的时代，为你的组件创建这种封装的环境对我来说更有意义(最重要的是保持一致，并使用让你舒服的东西😁).为你拥有的每个组件添加一个**测试**文件夹，这在一个大的代码库中，有许多组件和组件变体，看起来就像是没有干的事情。此外，我没有发现添加该文件夹对个人有任何好处。此外，当 jest 抓取你的根目录并寻找要运行的文件时，它并不特别寻找一个文件夹(嗯，取决于你的 jest 的正则表达式模式)。

## 命名和大小写约定:

```
- PascalCase for component file name and folder name
- Generally, I want to indicate if my components are container or component.
  Containers will usually be class components that contain state and logic,
  whereas components will house the actual content, styling and receive props from the container. 
  Example:
  - `MyComponent.container.js`
  - `MyComponent.component.js`
  - `MyComponent.jsx`          // if no container
  - `MyComponent.styles.js`
- lowerCamelCase for Higher Order Component file and folder name
- lowercase for all other root directory folders. For example: `src`, `components`, `assets` 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 一些值得注意的约定

## 形容方法:

```
describe('My component', () => {
  // group of test()
}) 
```

Enter fullscreen mode Exit fullscreen mode

Describe 方法是 jest 所谓的全局方法之一，您不必导入或要求使用它。describe 语句尤其用于将相似测试分组在一起。

## 测试方法

```
test('some useful message', () => {
   // logic
}, timeout) // timeout is optional 
```

Enter fullscreen mode Exit fullscreen mode

测试功能是面包和黄油。这是实际运行测试的函数。根据 Jest 的文档，第一个参数是测试的名称，第二个参数是添加测试逻辑(断言等)的回调。)，第三个参数是可选的，即超时。

测试函数还有一个别名，可以互换使用它():`it('test', () => {})`

* * *

## 《玩笑话入门》和 RTL 与 CRA:

完全公开。我喜欢使用 CRA it 为您设置一切，并减少随着时间的推移，您将获得的依赖版本落后的技术开销。使用 react-scripts，您几乎只需要担心这一部分。

`npx create-react-app ProjectName`

`npx create-react-app ProjectName --typescript`

我马上做的第一件事是安装所需的依赖项:

`npm install --save-dev @testing-library/jest-dom`

`npm install --save-dev @testing-library/react`

在`package.json`文件中，我添加了以下脚本:

```
"test": "jest -c jest.config.js --watch" 
```

Enter fullscreen mode Exit fullscreen mode

快速提示:当我开始一个新的 react 项目时，第一件事就是添加那些依赖+ `styled-components`和我的`types`(如果需要的话)。

测试库文档将 jest-dom 定义为 React 测试库的配套库，为 jest 提供定制的 dom 元素匹配器。本质上，是依赖关系提供了像`toHaveStyles`或`toHaveAttribute`这样的语句(或匹配器*)。

例子:
`expect(Component).toBeInTheDocument()`<——匹配者

一旦你的项目被创建，在我的 src 文件夹中我会添加一个名为`setupTests.js`的文件。

```
- src
  - components
  - App.js
  - setupTests.js 
```

Enter fullscreen mode Exit fullscreen mode

在环境中安装测试框架之前执行`setupFiles`。对于我们的例子，这尤其重要，因为它允许我们在测试执行之前运行正确的导入。这给了我们增加几个进口的机会。

所以在你的 setupTests.js 文件中:

```
import '@testing-library/jest-dom/extend-expect' 
```

Enter fullscreen mode Exit fullscreen mode

那个文件就这样了:)。

这就是你使用`jest`和`react-testing-library`开始运行所需要的一切！

* * *

## 从头开始使用 React 应用程序使用 jest 和 RTL:

这一部分会稍微长一点，因为有更多的工具要介绍和配置。在某种程度上，我们将一步一步地从头开始构建 react 应用程序。确实抽象了很多配置复杂性，而且做得很好，现在我们必须配置我们的巴别塔，对于我们的情况，最重要的是 jest 配置。jest 配置负责确保`jest`知道在哪里寻找、寻找什么以及如何执行它。

从头开始设置 React 应用程序的绝佳资源:
[https://blog . bitsrc . io/setting-a-React-project-from-scratch-using-babel-and-web pack-5f 26 a 525535d](https://blog.bitsrc.io/setting-a-react-project-from-scratch-using-babel-and-webpack-5f26a525535d)

## 目录结构

```
- node_modules`
- public
  - index.html
- src
  - components
    - MyComponent
      - MyComponent.jsx
      - MyComponent.styles.js
      - MyComponent.test.js      // here is what I do different
    - index.js             // source of truth for component export
  - utils
  - pages
  - App.jsx
  - App.test.jsx
  - App.styles.js
  - store.js
  - index.js
- webpack.config.js
- jest.config.js
- .gitignore
- .eslintrc
- .prettierrc 
```

Enter fullscreen mode Exit fullscreen mode

## index.html:

```
<!DOCTYPE html>
  <html lang="en">    
    <head>        
      <meta charset="UTF-8" />        
      <meta name="viewport" content="width=device-width, initial-scale=1.0" />        
      <meta http-equiv="X-UA-Compatible" content="ie=edge" />                                    
      React JS + Webpack
    </head>    
    <body>        
      <div id="root"></div>    
    </body>
  </html> 
```

Enter fullscreen mode Exit fullscreen mode

## App.js

```
import React from 'react';

const App = () => <h1>Hi World</h1>;

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

## index.js

```
import React from "react";
import ReactDOM from "react-dom";
import App from "./App";

ReactDOM.render(<App />, document.getElementById("root")); 
```

Enter fullscreen mode Exit fullscreen mode

## webpack.config.js:

```
const webpack = require("webpack");

// plugins
const HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
  entry: "./src/index.js",
  output: {
    filename: "./main.js"
  },
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      },
      {
        test: /\.(png|svg|jpg|gif)$/,
        use: ["file-loader"]
      },
      { test: /\.jsx$/, loader: "babel-loader", exclude: /node_modules/ },
      { test: /\.css$/, use: ["style-loader", "css-loader"] }
    ]
  },
  devServer: {
    contentBase: "./dist",
    hot: true
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: "./public/index.html",
      filename: "./index.html"
    }),
    new webpack.HotModuleReplacementPlugin()
  ]
}; 
```

Enter fullscreen mode Exit fullscreen mode

## jest.config.js:

```
module.export = {
  roots: ['<rootDir>/src'],
  transform: {
    '\\.(js|jsx)?$': 'babel-jest',
  },
  testMatch: ['<rootDir>/src/**/>(*.)test.{js, jsx}'], // finds test
  moduleFileExtensions: ['js', 'jsx', 'json', 'node'],
  testPathIgnorePatterns: ['/node_modules/', '/public/'],
  setupFilesAfterEnv: [
    '@testing-library/jest-dom/extend-expect'', '@testing-library/react/cleanup-after-each' ] // setupFiles before the tests are ran
}; 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## MyComponent.js:

```
import React from 'react'
import styled from 'styled-components'

const MyComponent = props => {

  return (
    <h1>`Hi ${props.firstName + '  ' + props.lastName}!`</h1>
  )
}
export default MyComponent 
```

Enter fullscreen mode Exit fullscreen mode

## [T1】my component . test . js:](#mycomponenttestjs)

```
import React from 'react'
import { render, cleanup } from '@testing-library/react'
import MyComponent from './MyComponent'

afterEach(cleanup)

describe('This will test MyComponent', () => {
  test('renders message', () => {
     const { getByText }= render(<Mycomponent 
                                 firstName="Alejandro"
                                 lastName="Roman"
                              />)

     // as suggested by Giorgio Polvara a more idiomatic way:
     expect(getByText('Hi Alejandro Roman')).toBeInTheDocument()
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 输入示例:

```
import React from 'react'
import { render, fireEvent } from '@testing-library/react'
import Input from './Input'

test('accepts string', () => {
  // I try to avoid using data-testid as that is not something a user would
  // use to interact with an element. There are a lot of great query and get 
  // methods
  const { getByPlaceholderText } = render(<Input placeholder="Enter
                                           Text" />);
  const inputNode = getByPlaceholderText('Search for a problem or application name');

  expect(inputNode.value).toMatch('') //tests input value is empty

  // if you need to perform an event such as inputing text or clicking
  // you can use fireEvent
  fireEvent.change(inputNode, { target: { value: 'Some text' } }));

  expect(inputNode.value).toMatch('Some text'); // test value 
                                                // is entered
}); 
```

Enter fullscreen mode Exit fullscreen mode

# 打字稿配置

## tsconfig.json:

```
{
  "include": [
    "./src/*"
  ],
  "compilerOptions": {
    "lib": [
      "dom",
      "es2015"
    ],
  "jsx": "preserve",
  "target": "es5",
  "allowJs": true,
  "skipLibCheck": true,
  "esModuleInterop": true,
  "allowSyntheticDefaultImports": true,
  "strict": true,
  "forceConsistentCasingInFileNames": true,
  "module": "esnext",
  "moduleResolution": "node",
  "resolveJsonModule": true,
  "isolatedModules": true,
  "noEmit": true
  },
  "include": ["./src/**/*"],    
  "exclude": ["./node_modules", "./public", "./dist", "./.vscode"]
} 
```

Enter fullscreen mode Exit fullscreen mode

## jest 配置:

```
module.exports = {
  roots: ['<rootDir>/src'],
  transform: {
    '\\.(ts|tsx)?$': 'babel-jest',
  },
  testMatch: ['<rootDir>/src/**/?(*.)test.{ts,tsx}'],   // looks for your test
  moduleFileExtensions: ['ts', 'tsx', 'js', 'jsx', 'json', 'node'],
  testPathIgnorePatterns: ['/node_modules/', '/public/'],
  setupFilesAfterEnv: [
    'jest-dom/extend-expect',
    '@testing-library/react/cleanup-after-each'
  ]  // sets ut test files
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 网页包配置:

```
const path = require('path')

// Plugins
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
    entry: {
        dev: './src/index.tsx',
    },
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'js/[name].bundle.js',
    },
    devServer: {
        compress: true,
        port: 3000,
        hot: true,
    },
    devtool: 'source-map',
    resolve: {
        extensions: ['.ts', '.tsx', '.js', '.jsx'],
    },
    module: {
        rules: [
            /**
             * Gets all .ts, .tsx, or .js files and runs them through eslint
             * and then transpiles them via babel.
             */
            {
                test: /(\.js$|\.tsx?$)/,
                exclude: /(node_modules|bower_components)/,
                use: ['babel-loader'],
            },

            /**
             * All output '.js' files will have any sourcemaps re-processed by
             * source-map-loader.
             */
            { test: /\.js$/, enforce: 'pre', loader: 'source-map-loader' },
        ],
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: './public/index.html',
        }),
    ],
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

额外资源:

一些资源帮助我学习使用 React 测试库的不同部分:

文档:

[https://testing-library . com/docs/react-testing-library/intro](https://testing-library.com/docs/react-testing-library/intro)

创建-反应-应用:[https://www.youtube.com/watch?v=Yx-p3irizCQ&t = 266s](https://www.youtube.com/watch?v=Yx-p3irizCQ&t=266s)

测试冗余:[https://www.youtube.com/watch?v=h7ukDItVN_o&t = 375s](https://www.youtube.com/watch?v=h7ukDItVN_o&t=375s)

组件单元测试:[https://www.youtube.com/watch?v=KzeqeI046m0&t = 330s](https://www.youtube.com/watch?v=KzeqeI046m0&t=330s)

嘲讽和更多组件测试:[https://www.youtube.com/watch?v=XDkSaCgR8g4&t = 580s](https://www.youtube.com/watch?v=XDkSaCgR8g4&t=580s)

传送门:[https://www.youtube.com/watch?v=aejwiTIBXWI&t = 1s](https://www.youtube.com/watch?v=aejwiTIBXWI&t=1s)

嘲讽:[https://www.youtube.com/watch?v=9Yrd4aZkse8&t = 567s](https://www.youtube.com/watch?v=9Yrd4aZkse8&t=567s)

测试异步组件:[https://www.youtube.com/watch?v=uo0psyTxgQM&t = 915s](https://www.youtube.com/watch?v=uo0psyTxgQM&t=915s)