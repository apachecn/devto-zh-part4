# 如何使用 NodeJS 和 Commander.js 创建您的命令行程序(CLI)

> 原文：<https://dev.to/ngduc/how-to-create-a-command-line-npm-module-cli-using-commander-js-ke4>

[![image](img/0cd449eade69187ef9858b2fb878cf95.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FBhT6PcX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AiuVFluyU2hg8UQXloU6BIQ.jpeg)

这篇文章将向你展示如何使用 Commander.js 模块创建命令行 npm 模块(CLI)。

Commander.js 是一个非常受欢迎的模块，可以让您创建自己的 CLI 程序。

首先，开始你的新项目——假设我的项目名是“JSON-now”

```
$ git clone https://github.com/yourname/json-now.git
$ cd json-now 
```

现在，创建您的 package.json 文件:

```
{
  "name": "json-now",
  "version": "0.0.1",
  "bin": {
    "json-now": "./bin/index.js"
  },
  "dependencies": {
    "commander": "^3.0.1"
  }
} 
```

然后，安装依赖项:

```
$ npm install 
```

“bin”部分指定您的命令行名称。如您所见，继续创建一个包含“index.js”文件的“bin”目录:

```
#!/usr/bin/env node

const program = require('commander');
const ver = require('../lib/ver');
program
  .usage('[options] <file>')
  .option('-v, --version', 'show version', ver, '')
  .option('-p, --port <port>', 'use custom port')
  .option('-f, --flag', 'boolean flag', false)
  .action((file, options) => {
    console.log('file name: ', file);
    // more hanlder: require('../lib/moreHandler')(options);
  })
  .parse(process.argv); 
```

让我们创建第一个名为“-v”或“- version”的选项，它显示版本号。在这里创建一个名为“lib”的目录和一个新文件“ver . js”:

```
const package = require('../package.json')
module.exports = () => {
    console.log(package.version);
}; 
```

到目前为止，它看起来直截了当。您创建了一个 commander“程序”，它通过运行“ver.js”来处理类似“-v”的选项

打开终端并试用:

```
$ node bin/index.js -v
0.0.1
$ node bin/index.js sample.json
file name: sample.json 
```

现在，是时候发布您的命令行供全世界使用了！

```
$ npm login
$ npm publish

Try out your shiny new command:
$ npm install json-now -g
$ json-now -v 
```

上述代码位于此处，供您参考:

[https://github.com/ngduc/api-now](https://github.com/ngduc/api-now)