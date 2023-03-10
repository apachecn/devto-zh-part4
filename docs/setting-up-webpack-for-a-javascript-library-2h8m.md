# 为 JavaScript 库设置 Webpack

> 原文：<https://dev.to/_hridaysharma/setting-up-webpack-for-a-javascript-library-2h8m>

几天前，我决定学习如何编写一个简单的 javascript 库。我想做一个 javascript 库，里面有几个函数，任何人都可以像 jquery 一样使用。

我决定使用 webpack 进行捆绑。我设置了 webpack，但是当我在代码中嵌入我的捆绑脚本时，我无法使用我想在库中提供的任何功能。

```
<script src="myLibrary.js"></script>
<script type="text/javascript">
 /* 
 using any of my library functions here gave me reference error.  
 */
</script> 
```

我知道我正在尝试做像 Redux、jquery 等其他库所做的事情。但是我不明白他们是怎么做到的。因此，我深入研究了 webpack，以了解如何做到这一点。

我创建了一个小项目来演示我是如何做到的。github repo 可在以下网址找到

## ![GitHub logo](img/04febf72106d337f3956ebfaf9121dae.png)[【vyasiday】](https://github.com/vyasriday)/[【web pack-js-library】](https://github.com/vyasriday/webpack-js-library)

### 用于编写 javascript 库并使其作为 scirt 或 npm 包可用的 webpack 设置

<article class="markdown-body entry-content" itemprop="text">

# 为编写自己的 JavaScript 库而设置的 Webpack

## 如何让项目运作起来

### 1.克隆存储库

### 2.npm 安装

### 3.npm 运行构建

【dist 目录内生成了一个 index.js。将其作为外部脚本添加到您的任何项目中。 *嵌入捆绑文件后，可以在代码中的$ in 上访问任何方法。*

例如，你可以用$。在您的 javascript 中使用大写方法

jest 使用 babelrc 进行代码翻译。

</article>

[View on GitHub](https://github.com/vyasriday/webpack-js-library)

我创建了一个 **src** 目录，其中存放了我所有的源代码。 ***index.js*** 是我项目的参赛文件。

*   *src*
    *   *index.js*
    *   *capital . js*
    *   *unique.js*
    *   *区分环. js*

***web pack . config . js***

```
const path = require('path');

module.exports = {
  entry: path.resolve(__dirname, 'src/index.js'),  
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'index.js',
    library: '$',
    libraryTarget: 'umd',
  },
  module: {
    rules: [
      {
        test: /\.(js)$/,
        exclude: /node_modules/,
        use: ['babel-loader'],
      },
    ],
  },
  resolve: {
    extensions: ['.js'],
    modules: [path.resolve(__dirname, 'src')],
  },
  mode: 'development',
  devtool: 'sourceMap',
}; 
```

在 *webpack 配置*中，**输出**中有两个重要属性用于捆绑 javascript 库。

*   ***库:' $'***
*   ***库目标:“UMD”**T3】*

**库**是*变量*的名称，代码可以访问为。例如，jquery 以$的形式提供，就像这样。任何功能都可以像*一样访问。name_of_method* 和 **libraryTarget** 是将被公开的库。我使用 *babel-loader* 通过 webpack 进行代码转换。运行构建脚本后，捆绑文件被放入 **dist** 目录。

```
{  "name":  "webpack-js-library",  "jest":  {  "roots":  [  "test/"  ]  },  "version":  "1.0.0",  "main":  "dist/index.js",  "scripts":  {  "build":  "webpack",  "test":  "jest"  },  "homepage":  "https://github.com/vyasriday/webpack-js-library#readme",  "devDependencies":  {  "@babel/core":  "^7.5.5",  "@babel/preset-env":  "^7.5.5",  "babel-eslint":  "^10.0.2",  "babel-loader":  "^8.0.6",  "eslint":  "^6.1.0",  "jest":  "^24.9.0",  "webpack":  "^4.39.2",  }  } 
```

在 *package.json* 中有一个重要的属性 **main** 。package.json 的主要属性是指向 package.json 描述的模块的入口点的方向。这里我想让它指向这个捆绑文件，它是这个库的编译代码。

我还将 jest 用于一个基本的测试设置。对图书馆进行测试是有好处的。

**src/index . js**T2】

```
import capitalize from './capitalize';
import unique from './unique';
import longestDistinctSubstring from './distinctString';

export { capitalize, unique, longestDistinctSubstring }; 
```

重要的是，您必须导出您想要在库中显示的任何内容。这里我公开了库中的三个函数。在捆绑时，webpack 知道它应该将这些函数放在库中。

现在，我可以轻松地访问我的图书馆，就像-

```
<script src="dist/index.js"></script>
<script type="text/javascript">
  console.log($.capitalize('hridayesh'))
</script> 
```

这就是如何设置 webpack 来编写 javascript 库的方法。*