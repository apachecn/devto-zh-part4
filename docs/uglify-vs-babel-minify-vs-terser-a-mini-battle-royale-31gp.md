# 丑陋 vs 巴别塔-迷你 vs 特塞尔:一场迷你皇家战役

> 原文：<https://dev.to/bnevilleoneill/uglify-vs-babel-minify-vs-terser-a-mini-battle-royale-31gp>

[![](img/8b37cba21ea6f901ae48eaf3ef654733.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a3-2Tu8H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jjsnm3bj14ln7glioq58.jpg)

## 什么是缩小化？

缩小(也称最小化)是指在不改变功能的情况下，从解释编程语言(T0)或 T2 标记语言(T3)的源代码中删除所有不必要的字符。这些不必要的字符通常包括:

*   [空白字符](https://en.wikipedia.org/wiki/Whitespace_(computer_science))
*   [换行符](https://en.wikipedia.org/wiki/Newline)
*   [评论](https://en.wikipedia.org/wiki/Source_code_comment)
*   [块分隔符](https://en.wikipedia.org/wiki/Curly_bracket_programming_language)

我们试着用一个例子来理解这一点。下面的代码展示了一个示例 JavaScript 代码，用于创建一个数组并用前 20 个整数值初始化它:

```
var array = [];
for (var i = 0; i < 20; i++) {
  array[i] = i;
} 
```

现在，让我们试着手动缩小这段代码。下面的例子展示了我们如何用一行代码实现同样的功能:

```
for(var a=[i=0];++i<20;a[i]=i); 
```

首先，我们简化了`array`变量的名称(`array`到`a`)，然后我们将它移到了`for()`循环初始化构造中。我们还将第 3 行的数组初始化移到了`for()`循环中。因此，文件的字符数和大小都大大减少了。

## 为什么要缩小？

既然我们理解了什么是缩小，那么就很容易猜测我们为什么要这样做。因为缩小缩小了源代码的大小，所以它在网络上的传输变得更加有效。

这对于 web 和移动应用程序尤其有用，在这些应用程序中，前端向后端发出 http 请求，以获取文件、用户数据等资源。对于 Instagram 或脸书等相当大的应用程序，前端通常安装在用户的设备上，而后端和数据库作为多个实例存在于内部服务器或云中。

在一个典型的用户操作中，比如加载一张照片，前端向后端发出 http 请求，后端则向数据库实例发出请求以获取所请求的资源。这涉及到通过网络传输数据，这个过程的效率与传输的数据大小成正比。这正是缩小有用的地方。

[![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)](https://logrocket.com/signup/)

## 那么我该如何缩小呢？

在上一节中，我们看到了如何手工缩小一个微不足道的代码。但是对于庞大的代码库来说，这真的不是一个可扩展的解决方案。多年来，已经开发了各种工具来精简 JavaScript 代码。让我们看看最受欢迎的几个:

### 丑行

[UglifyJS](https://github.com/mishoo/UglifyJS2) 的目标是缩小和压缩代码。让我们继续使用下面的命令安装它:

```
npm install uglify-js -g 
```

现在让我们尝试在 JavaScript 模块上运行 uglify。为此，我编写了一个示例模块`sample.js`，代码如下:

```
var print = "Hello world! Let's minify everything because, less is more"

var apple = [1,2,3,4,5]

for(let element in apple) {
    console.log(print)
    console.log(element)
}

for(let element in apple) {
    console.log(print)
    console.log(element)
}

for(let element in apple) {
    console.log(print)
    console.log(element)
}

for(let element in apple) {
    console.log(print)
    console.log(element)
}

for(let element in apple) {
    console.log(print)
    console.log(element)
}

for(let element in apple) {
    console.log(print)
    console.log(element)
}

for(let element in apple) {
    console.log(print)
    console.log(element)
}

for(let element in apple) {
    console.log(print)
    console.log(element)
}

for(let element in apple) {
    console.log(print)
    console.log(element)
}

for(let element in apple) {
    console.log(print)
    console.log(element)
}

for(let element in apple) {
    console.log(print)
    console.log(element)
}

for(let element in apple) {
    console.log(print)
    console.log(element)
}

for(let element in apple) {
    console.log(print)
    console.log(element)
}

for(let element in apple) {
    console.log(print)
    console.log(element)
} 
```

这段代码基本上是在一个`for()`循环中打印一个字符串和一个数组。我们多次复制`for()`循环，以增加文件的大小，这样我们可以更好地看到丑陋的效果。

[![Sample.js File Size](img/9008b1c792530356918f0d39f6486ee6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1-gH21X3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/blog.logrocket.com/wp-content/uploads/2019/06/samplejs-file-size.png%3Fresize%3D1038%252C197%26ssl%3D1) 

<figcaption id="caption-attachment-2460">文件大小为 1.2KB.</figcaption>

[![UglifyJS Options](img/03d767a144722b77a477e8a1aef2048b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kcQj53u0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2019/06/uglifyjs-options-2.png%3Fresize%3D2184%252C998%26ssl%3D1) 

<figcaption id="caption-attachment-2443">丑 JS 选项。</figcaption>

正如我们所看到的，Uglify 有很多选项，其中大部分是不言自明的。所以，让我们继续尝试其中的几个:

[![Sample-uglify.js File Output](img/52c39646e702e2e12500d82e97b3fea3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7CRLqKck--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/blog.logrocket.com/wp-content/uploads/2019/06/sample-uglify.js-output.png%3Fresize%3D1108%252C504%26ssl%3D1) 

<figcaption id="caption-attachment-2429">文件大小为 944B，通过反复打印字符串和数组值来执行。</figcaption>

我们在`sample.js`文件中使用了`-c`(压缩)和`-m`(变形)选项，并对其进行了美化。文件大小已经减少到 944B，大约减少了 22%。现在，让我们看看文件内容，看看它是如何随着丑化而改变的:

```
var print="Hello world! Let's minify everything because, less is more",apple=[1,2,3,4,5];for(i=0;i<5;i++)console.log(print),console.log(apple[i]);for(i=0;i<5;i++)console.log(print),console.log(apple[i]);for(i=0;i<5;i++)console.log(print),console.log(apple[i]);for(i=0;i<5;i++)console.log(print),console.log(apple[i]);for(i=0;i<5;i++)console.log(print),console.log(apple[i]);for(i=0;i<5;i++)console.log(print),console.log(apple[i]);for(i=0;i<5;i++)console.log(print),console.log(apple[i]);for(i=0;i<5;i++)console.log(print),console.log(apple[i]);for(i=0;i<5;i++)console.log(print),console.log(apple[i]);for(i=0;i<5;i++)console.log(print),console.log(apple[i]);for(i=0;i<5;i++)console.log(print),console.log(apple[i]);for(i=0;i<5;i++)console.log(print),console.log(apple[i]);for(i=0;i<5;i++)console.log    (print),console.log(apple[i]);for(i=0;i<5;i++)console.log(print),console.log(apple[i]);for(i=0;i<5;i++)console.log(print),console.log(apple[i]); 
```

从上面的示例中，我们可以看到输出的内容具有相同的代码，没有任何空格和换行符。

为了进一步了解丑陋的影响，让我们用一个原型函数编写一个示例 JS 代码:

```
// comments 

function sample(helloworld) {
  this.hello = helloworld
}
sample.prototype.printvar = function()
{
  console.log(this.hello)
}

var hello = "hello world"
var s = new sample(hello)
s.printvar() 
```

现在，让我们丑化这段代码:

[![Uglified Code With Enclose Option](img/00a75eebe76ee65579deecfa8952742d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---j7aLoOa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/blog.logrocket.com/wp-content/uploads/2019/06/uglified-code-with-enclose-1.png%3Fresize%3D1558%252C244%26ssl%3D1) 

<figcaption id="caption-attachment-2436">丑化码是 131B。</figcaption>

注意，我使用了 Uglify 的`-e`(括起来)选项。该选项将所有内容嵌入到一个大函数中，带有可配置的参数和值。为了理解这意味着什么，我们来看看输出的内容:

```
!function(){function o(o){this.hello=o}o.prototype.printvar=function(){console.log(this.hello)};new o("hello world").printvar()}(); 
```

在经过丑化的输出中，我们可以看到函数名`sample`不见了，取而代之的是`o`。所有的代码都包含在一个大函数中，这以牺牲可读性为代价进一步减小了代码的大小。

原始文件大小为 207B，运行 Uglify 后，文件大小减少到 131B。与不使用`-e`选项时的 22%相比，减少了近 40%。

现在，让我们看看另一个流行的迷你:巴别迷你。

### 通天塔(又名巴比里)

babel-minify ，原名 [Babili](https://babeljs.io/blog/2016/08/26/babili) ，是一个实验项目，试图使用 [Babel 的](https://babeljs.io/)工具链(用于编译)来做类似的事情:缩小。目前在 0.x 中，官方资源库不建议在生产中使用。

当我们已经有丑陋的东西时，为什么我们还需要这个工具？如果您注意到前面的例子，我没有使用最新版本 ECMAScript 的语法。这是因为 [Uglify 还不支持它](https://babeljs.io/blog/2016/08/26/babili#current-minifiers)——但是 babel-minify 可以。

这是因为它只是一套 Babel 插件，Babel 已经用解析器 [Babylon](https://github.com/babel/babylon) 理解了新的语法。此外，当有可能只针对支持较新的 ES 特性的浏览器时，您的代码可以更小，因为您不必先转换文件，然后再缩小它。

在 babel-minify 之前，我们会运行 babel 来转换 ES6，然后运行 Uglify 来缩小代码。有了 babel-minify，这个两步过程实质上变成了一步。

babel-minify 具有 ES2015+意识，因为它是使用 babel 工具链构建的。它被写成一套巴别塔插件，可以用[巴别塔预置缩小](https://github.com/babel/minify/tree/master/packages/babel-preset-minify)来消费。让我们看一个例子。

让我们使用命令:
在本地安装 Babel 和用于传输 ES6 的 Babel 预置

```
npm install --save-dev @babel/core @babel/cli
npm install --save-dev babel-plugin-transform-es2015-classes 
```

现在，让我们用 ES6 语法写一个`sample.js`:

```
//ES6 Syntax

class sample {
  constructor(helloworld) {
      this.hello = helloworld
  }
  printvar() {
    console.log(this.hello)
  }
}

var hello = "hello world"
var s = new sample(hello)
s.printvar() 
```

让我们使用下面的命令来传输这段代码:

```
babel --plugins transform-es2015-classes sample.js > sample-transpiled.js 
```

转换后的代码内容如下:

```
function _classCallCheck(instance, Constructor) { if (!(instance instanceof Constructor)) { throw new TypeError("Cannot call a class as a function"); } }

function _defineProperties(target, props) { for (var i = 0; i < props.length; i++) { var descriptor = props[i]; descriptor.enumerable = descriptor.enumerable || false; descriptor.configurable = true; if ("value" in descriptor) descriptor.writable = true; Object.defineProperty(target, descriptor.key, descriptor); } }

function _createClass(Constructor, protoProps, staticProps) { if (protoProps) _defineProperties(Constructor.prototype, protoProps); if (staticProps) _defineProperties(Constructor, staticProps); return Constructor; }

//ES6 Syntax
let sample = function () {
  function sample(helloworld) {
    _classCallCheck(this, sample);

    this.hello = helloworld;
  }

  _createClass(sample, [{
    key: "printvar",
    value: function printvar() {
      console.log(this.hello);
    }
  }]);

  return sample;
}();

var hello = "hello world";
var s = new sample(hello);
s.printvar(); 
```

正如您所看到的，ES6 类语法已经被转换成了常规的函数语法。现在，让我们运行丑化这个内容来缩小它:

```
uglifyjs sample-transpiled.js -c -m -e -o sample-transpiled-uglified.js 
```

现在，这个输出的内容看起来像这样:

```
function \_classCallCheck(e,l){if(!(e instanceof l))throw new TypeError("Cannot call a class as a function")}function \_defineProperties(e,l){for(var n=0;n<l.length;n++){var r=l[n];r.enumerable=r.enumerable||!1,r.configurable=!0,"value"in r&&(r.writable=!0),Object.defineProperty(e,r.key,r)}}function \_createClass(e,l,n){return l&&\_defineProperties(e.prototype,l),n&&\_defineProperties(e,n),e}let sample=function(){function e(l){\_classCallCheck(this,e),this.hello=l}return \_createClass(e,[{key:"printvar",value:function(){console.log(this.hello)}}]),e}();var hello="hello world",s=new sample(hello);s.printvar(); 
```

如果我们比较文件大小，`sample.js`是 227B，`sample-transpiled.js`是 1KB，`sample-transpiled-uglified.js`是 609B。这显然不是一个最佳的过程，因为它会导致文件大小的增加。为了解决这个问题，引入了 babel-minify。现在，让我们安装 babel-minify 并尝试编译代码。

```
npm install babel-minify --save-dev 
```

让我们使用下面的命令用 babel-minify 来缩小相同的`sample.js`代码:

```
minify sample.js > sample-babili.js 
```

我们来看看这个输出的内容:

```
class sample{constructor(l){this.hello=l}printvar(){console.log(this.hello)}}var hello="hello world",s=new sample(hello);s.printvar(); 
```

这个文件的大小是 135B，减少了将近 40 %,这是一个更好的缩减代码的方法。它直接提高了通过网络传输的效率，并且可以在浏览器上运行，因为 Babel 可以传输代码。也有[各种插件可用](https://babeljs.io/blog/2016/08/26/babili#some-of-the-plugins-https-githubcom-babel-babili-plugins-in-babel-preset-babili)。

### 简洁

Terser 是一个用于 ES6+的 JavaScript 解析器和分解器/压缩器工具包，可能是最有效的一个。Terser 建议你使用 [Rollup](https://rollupjs.org/guide/en/) 来捆绑你的模块，因为这样会产生更小的代码。

Rollup 是一个类似于 [webpack](https://webpack.js.org/) 的模块捆绑器，它被创建来尽可能高效地构建 JavaScript 库的平面分发版，利用 ES2015 模块的巧妙设计。

虽然 Rollup 是一个不错的选择，但是如果您使用的是 webpack >v4，那么默认情况下会使用 Terser。可以通过切换一个[布尔](https://webpack.js.org/configuration/optimization/)变量来启用 Terser，比如:

```
module.exports = {
  //...
  optimization: {
    minimize: false
  }
}; 
```

让我们安装 Terser:

```
npm install terser -g 
```

Terser 命令行的语法如下:

```
terser [input files] [options] 
```

Terser 可以接受多个输入文件。建议您首先传递输入文件，然后传递选项。Terser 将按顺序解析输入文件，并应用任何压缩选项。

这些文件在同一个全局范围内被解析——也就是说，从一个文件到另一个文件中声明的某个变量/函数的引用将被正确匹配。如果没有指定输入文件，Terser 将从 STDIN 中读取。

如果您希望在输入文件之前传递您的选项，请用双破折号分隔两者，以防止输入文件被用作选项参数:

```
terser --compress --mangle -- input.js 
```

现在让我们试着运行我们的`sample.js`代码:

```
terser -c toplevel,sequences=false --mangle -- sample.js > sample-terser.js 
```

以下是该输出的内容:

```
new class{constructor(l){this.hello=l}printvar(){console.log(this.hello)}}("hello world").printvar(); 
```

正如我们所看到的，输出是迄今为止我们看到的所有工具中最好的。文件的大小是 102B，比原来的`sample.js`大小减少了将近 55%。使用`--help`选项可以找到 Terser 的其他命令行选项。

[![Terser Command Line Options](img/e0e5766fe6bb27c5f4aa3f1233d618b6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---e24y89h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2019/06/terser-options.png%3Fresize%3D1600%252C783%26ssl%3D1) 

<figcaption id="caption-attachment-2442">简洁的命令行选项。</figcaption>

在所有选项中，我们最感兴趣的是`--compress`和`--mangle`，它们各有一套选项。`--compress`和`--mangle`的[选项](https://github.com/terser-js/terser#compress-options)让你控制如何处理你的源代码以生成缩小的输出。

如果你注意到了，我们已经在第一个简洁的例子中使用了`--compress`的`toplevel`和`sequences`选项。例如，您可以将`true`传递给`--compress`的`drop_console`选项，从源代码中删除所有的`console.*`函数，如果您不想破坏类名，也可以使用`keep_classnames`选项。

有时，美化生成的输出可能很有用。你可以用 [`--beautify`](https://github.com/terser-js/terser#output-options) 选项来完成。许多构建工具使用 Terser — [在这里找到它们](https://www.npmjs.com/browse/depended/terser)。

让我们尝试在源文件中使用`drop_console`选项，看看`console.log()`函数是否被删除:

```
terser --compress drop\_console=true -- sample.js > sample-drop-console.js 
```

现在，让我们看看源代码的内容，`sample.js` :

```
//ES6 Syntax

class sample {
  constructor(helloworld) {
      this.hello = helloworld
  }
  printvar() {
    console.log(this.hello)
  }
}

var hello = "hello world"
var s = new sample(hello)
s.printvar() 
```

现在输出，`sample-drop-console.js` :

```
class sample{constructor(helloworld){this.hello=helloworld}printvar(){}}var hello="hello world",s=new sample(hello);s.printvar(); 
```

如您所见，`console.log(this.hello)`调用已经从输出中移除。现在，让我们用`toplevel`和`sequences`选项来试试`drop_console`:

```
terser -c toplevel,sequences=false,drop\_console=true --mangle -- sample.js > sample-terser-tsd.js 
```

我们来看看内容:

```
new class{constructor(r){this.hello=r}printvar(){}}("hello world").printvar(); 
```

正如我们所看到的，代码被进一步破坏和压缩，这个新文件的大小只有 79B。与不使用`drop_console`选项时我们看到的 55%相比，这减少了 65%。这样，我们可以根据项目的需求，使用选项来平衡可读性和性能。

## 性能对比

到目前为止，我们已经研究了三种最流行的缩小 JS 和 CSS 文件的工具。来自 Babel 存储库的比较基准测试结果提供了这些工具的一些统计数据，可以帮助您为您的项目选择正确的缩小器:

[![Minifier Benchmark Results For React.js](img/64ec5bc22923660f3036f34d58c124f4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NOVPnHWz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/blog.logrocket.com/wp-content/uploads/2019/06/minifier-benchmark-results-react.png%3Fresize%3D1766%252C698%26ssl%3D1)

正如我们所看到的，Terser 在基于 React 的项目中表现最好。你可以在这里找到其他 web 框架的结果。

## 为 React 配置缩小器

在这一节中，我们将了解为 React 应用程序配置迷你化器的过程。让我们在这个例子中使用 Terser 来缩小 React 应用程序。为了以简化的方式实现这一点，我们使用了 [webpack](https://webpack.js.org/) 。webpack 是一个工具链，用于将所有文件打包成一个名为`bundle.js`的文件，可以高效地加载。让我们使用下面的命令安装 web pack:

```
npm install webpack --save-dev 
```

让我们也安装一些巴别塔插件，以便传输代码:

```
npm install babel-core babel-loader babel-preset-env babel-preset-react\babel-preset-stage-0 --save-dev 
```

现在，让我们安装 [Terser](https://github.com/webpack-contrib/terser-webpack-plugin) 插件:

```
npm install terser-webpack-plugin --save-dev 
```

我们还需要。svg 和。css 加载器，所以我们也来安装吧:

```
npm install svg-inline-loader --save-dev
npm install css-loader --save-dev 
```

在这个阶段，我们只需要配置`webpack.config.js`文件，所以我们这样做:

```
const path = require('path');
const TerserPlugin = require('terser-webpack-plugin');

module.exports = {
  entry: "./src/index.js",
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  },
  mode: 'development',
  module: {
    rules: [
    {
      test: /\.js$/,
      exclude: /(node_modules)/,
      use: {
        loader: 'babel-loader',
        options:{
          presets: ['@babel/preset-react']
        }
      }
    },
    {
      test: /\.svg$/,
      loader: 'svg-inline-loader'
    },
    {
      test: /\.css$/,
      use: ['style-loader', 'css-loader'],
    }]
  },
  optimization: {
    minimizer: [new TerserPlugin()],
  },
} 
```

从上面的代码我们可以看到，webpack 的入口点是`src/`里面的`index.js`，最终的输出会作为`bundle.js`存储在`dist/`目录里面。**优化**字段将`TerserPlugin`拉入缩小过程。现在，让我们运行 webpack 来为生产静态构建我们的应用程序。

[![Webpack Output](img/4d9b35d0182cabe18295f16fb913cac0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PUI27HK8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/blog.logrocket.com/wp-content/uploads/2019/06/webpack-output.png%3Fresize%3D2186%252C910%26ssl%3D1) 

<figcaption id="caption-attachment-2445">输出 webpack。</figcaption>

我们可以看到，webpack 运行了所有文件的加载程序和插件，构建了一个 938KB 大小的`bundle.js`，而我们的整个应用程序要比这个大得多。这就是 webpack 以及相关加载器和插件的真正威力。

最近推出了一些新的捆扎机。其中，[卷装](https://rollupjs.org/guide/en/)和[包裹](https://parceljs.org/)越来越受欢迎。任何捆绑工具的基本配置和设置都与 webpack 相似。你可以在这里找到 webpack、Rollup 和 package[的性能对比。](https://x-team.com/blog/rollup-webpack-parcel-comparison/)

## 结论

最后，让我展示一个来自 [npm 趋势](https://www.npmtrends.com/babel-minify-vs-babel-minify-webpack-plugin-vs-uglifyjs-webpack-plugin-vs-terser)的片段来结束这篇文章。正如我们所看到的，Terser 在过去的六个月中获得了显著的流行。这可以直接归因于它比其他缩小工具更好的性能。

[![Minifier Downloads Comparison Graph](img/8df5864d0116f9002de6404c5e637cd5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5MxbZRDi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/blog.logrocket.com/wp-content/uploads/2019/06/minifier-downloads-comparison-graph.png%3Fresize%3D1600%252C914%26ssl%3D1)

感谢你阅读这篇文章。我希望你喜欢学习缩小，并澄清了一些长期以来对这个概念的疑虑。

* * *

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/0abf868fe5ccbed99d71cb8d9e81ed98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6FG5kvEL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2017/03/1d0cd-1s_rmyo6nbrasp-xtvbaxfg.png%3Fresize%3D1200%252C677%26ssl%3D1)

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[Uglify vs . Babel-minify vs . Terser:A mini battle royale](https://blog.logrocket.com/uglify-vs-babel-minify-vs-terser-a-mini-battle-royale/)首先出现在 [LogRocket 博客](https://blog.logrocket.com)上。