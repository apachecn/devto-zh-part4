# 如何在 express 上运行 webpack-dev-server

> 原文：<https://dev.to/riversun/how-to-run-webpack-dev-server-on-express-5ei9>

# 如何在 express 上运行 webpack-dev-server？

使用 webpack-dev-middleware 和 webpack-hot-middleware 的自动重载(实时重载)示例。

# 概述

■通过将 **webpack-dev-server** 包含在 **express** 中，您可以做以下事情！

*   当您更改前端源代码时，无需手动重新加载浏览器。
*   当您更改服务器端源代码时，无需手动重启服务器。

■库
在 express 上使用 webpack-dev-middleware 和 webpack-hot-middleware。

# 我们开始吧。

## (1)项目创建

从头开始创建一个项目，包括前端和服务器端。
用合适的名称创建一个目录，并创建为 npm 项目。

```
mkdir webpack-dev-server-on-express
cd webpack-dev-server-on-express
npm init 
```

Enter fullscreen mode Exit fullscreen mode

## (2)所需模块的安装

安装所需的模块

1.  安装 webpack 模块

```
npm install --save-dev webpack webpack-cli webpack-dev-server webpack-dev-middleware webpack-hot-middleware 
```

Enter fullscreen mode Exit fullscreen mode

1.  服务器模块的安装

```
npm install --save-dev express multer babel-watch 
```

Enter fullscreen mode Exit fullscreen mode

1.  前端模块的安装

```
npm install --save-dev @babel/core @babel/preset-env babel-loader core-js@3 
```

Enter fullscreen mode Exit fullscreen mode

## (3)制作前端脚本

**index.html**T2】

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    Addition
</head>
<body>

<h3>Addition</h3>
<form action="" method="post" id="myform">
    <input type="number" name="firstValue"><br>
    <span>+</span><br>
    <input type="number" name="secondValue"><br>
</form>
<span>=</span><br>
<input type="number" id="result" readonly><br><br>

<button id="btn-clac">Calc</button>
<script src="js/app.js"></script>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

**index.js**

```
const btnSend = document.querySelector('#btn-clac');

btnSend.addEventListener('click', evt => {

    const xhr = new XMLHttpRequest();

    xhr.addEventListener('load', evt => {

        if (xhr.status == 200) {
            const result = JSON.parse(xhr.response);
            const resultEle = document.querySelector('#result');
            resultEle.value = result.sum;
        }
    });

    xhr.addEventListener('error', evt => {
        console.error(evt);
    });

    xhr.open('post', 'api/add', true);

    const formEle = document.querySelector('#myform');
    const formData = new FormData(formEle);

    xhr.send(formData);

}); 
```

Enter fullscreen mode Exit fullscreen mode

**webpack.config.js**

```
const path = require('path');

module.exports = {
    mode: 'development',
    devServer: {
        contentBase: path.join(__dirname, 'public'),
        port: 8080,
        host: `localhost`,
    },
    entry: {
        app: [
            './src_client/index.js'
        ]
    },
    output: {
        path: path.join(__dirname, 'dist'),
        publicPath: '/js/',
        filename: `[name].js`,
    },
    module: {
        rules: [
            {
                test: /\.js$/,
                exclude: /(node_modules|bower_components)/,
                use: [
                    {
                        loader: 'babel-loader',
                        options: {
                            presets: [
                                [
                                    '@babel/preset-env',
                                    {
                                        'modules': 'false',//commonjs,amd,umd,systemjs,auto
                                        'useBuiltIns': 'usage',
                                        'targets': '> 0.25%, not dead',
                                        'corejs': 3
                                    }
                                ]
                            ]
                        }
                    }
                ]
            }
        ]
    },
    resolve: {
        alias: {}
    },
    plugins: [],

}; 
```

Enter fullscreen mode Exit fullscreen mode

## (4)制作服务器端脚本

**server.js**

```
const express = require('express');
const multer = require('multer');
const multipart = multer();

const webpack = require('webpack');
const webpackDevMiddleware = require('webpack-dev-middleware');
const webpackHotMiddleware = require('webpack-hot-middleware');
const config = require('../webpack.config.js');

const app = express();
const port = 8080;

const devServerEnabled = true;

if (devServerEnabled) {
    //reload=true:Enable auto reloading when changing JS files or content
    //timeout=1000:Time from disconnecting from server to reconnecting
    config.entry.app.unshift('webpack-hot-middleware/client?reload=true&timeout=1000');

    //Add HMR plugin
    config.plugins.push(new webpack.HotModuleReplacementPlugin());

    const compiler = webpack(config);

    //Enable "webpack-dev-middleware"
    app.use(webpackDevMiddleware(compiler, {
        publicPath: config.output.publicPath
    }));

    //Enable "webpack-hot-middleware"
    app.use(webpackHotMiddleware(compiler));
}

app.use(express.static('./public'));

//API
app.post('/api/add', multipart.any(), function (req, res) {

    //execute addition(tasizan)
    const firstValue = parseInt(req.body.firstValue);
    const secondValue = parseInt(req.body.secondValue);
    const sum = firstValue + secondValue;

    //return result
    res.json({sum: sum, firstValue: firstValue, secondValue: secondValue});

});

app.listen(port, () => {
    console.log('Server started on port:' + port);
}); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

```
const express = require('express');
const multer = require('multer');
const multipart = multer(); 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们导入 express 和 multer 来处理多部分表单数据。
当使用带有 XMLHttpRequest 的 POST 方法发送由“new FormData()”创建的表单对象时，数据以**“multipart/form-data”**编码。
所以我们可以使用“multer”而不是“body-parser”来解析 express 上的请求体。

* * *

```
const webpack = require('webpack');
const webpackDevMiddleware = require('webpack-dev-middleware');
const webpackHotMiddleware = require('webpack-hot-middleware');
const config = require('../webpack.config.js'); 
```

Enter fullscreen mode Exit fullscreen mode

将 webpack、webpack-dev-middleware、webpack-hot-middleware 导入 server . js .
**web pack . config . js**作为配置对象读取。

* * *

```
 //reload=true:Enable auto reloading when changing JS files or content
 //timeout=1000:Time from disconnecting from server to reconnecting
 config.entry.app.unshift('webpack-hot-middleware/client?reload=true&timeout=1000'); 
```

Enter fullscreen mode Exit fullscreen mode

它的意思和如下重载一样。

```
entry: {
    app: [
    'webpack-hot-middleware/client?reload=true&timeout=1000',
    './src_client/index.js'
    ]
}, 
```

Enter fullscreen mode Exit fullscreen mode

# (5)Starter script 成 package.json

将以下内容添加到 package.json.

```
"start": "babel-watch ./src_server/server.js" 
```

Enter fullscreen mode Exit fullscreen mode

**package . JSON**T2】

```
{  "name":  "webpack-dev-server-on-express",  "version":  "1.0.0",  "description":  "",  "main":  "index.js",  "scripts":  {  "start:client":  "webpack-dev-server --config webpack.config.js",  "start":  "babel-watch ./src_server/server.js"  },  "author":  "",  "license":  "ISC",  "devDependencies":  {  "webpack":  "^4.34.0",  "webpack-cli":  "^3.3.4",  "webpack-dev-server":  "^3.7.1",  "webpack-dev-middleware":  "^3.7.0",  "webpack-hot-middleware":  "^2.25.0",  "express":  "^4.17.1",  "multer":  "^1.4.1",  "babel-watch":  "^7.0.0",  "@babel/core":  "^7.4.5",  "@babel/preset-env":  "^7.4.5",  "babel-loader":  "^8.0.6",  "core-js":  "^3.1.4"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

启动服务器并尝试自动重新加载开发环境。

```
npm start 
```

Enter fullscreen mode Exit fullscreen mode

你可以在 [http://localhost:8080](http://localhost:8080) 上试试这个应用

# 提示

如果你有这样的错误。

```
WebpackOptionsValidationError: Invalid configuration object. Webpack has been initially used using a configuration object that does not match the API schema.
-Configuration should be an object. 
```

Enter fullscreen mode Exit fullscreen mode

当 webpack.config.js 的 module.exports 不返回**对象**而是返回**函数**时，就会出现这种情况。
例如，在下面的 webpack.config.js 中，lambda 函数被返回，以便它可以在启动时通过一个参数进行分支。
上述问题就发生在这种时候。

**webpack.config.js** (返回函数样式)

```
const path = require('path');
module.exports = (env, argv) => {
    const conf = {
        mode: 'development',
        devServer: {
            contentBase: path.join(__dirname, 'public'),
            port: 8080,
            host: `localhost`,
        },
        entry: {
            app: ['./src_client/index.js']
        },
        output: {
            path: path.join(__dirname, 'dist'),
            publicPath: '/js/',
            filename: `[name].js`,
        },
        resolve: {
            alias: {}
        },
        plugins: [],
    }
    return conf;
}; 
```

Enter fullscreen mode Exit fullscreen mode

所以你可以从这个
开始修改你的代码

```
const config = require('../webpack.config.js'); 
```

Enter fullscreen mode Exit fullscreen mode

敬这个。

```
const webpackConfigJs = require('../webpack.config.js');
const config = webpackConfigJs(); 
```

Enter fullscreen mode Exit fullscreen mode

# 源代码

[https://github.com/riversun/webpack-dev-server-on-express](https://github.com/riversun/webpack-dev-server-on-express)