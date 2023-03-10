# 编写简单的混淆和缩小系统

> 原文：<https://dev.to/zicsus/writing-simple-obfuscation-and-minification-system-3395>

混淆是使你的代码对人类来说不清楚和不可读的过程。这增加了源代码的安全性，特别是在源代码容易获得的网络世界中。缩小是指删除代码中不必要的数据，从而减小文件大小和加快加载速度的过程。在本教程中，我们将重点放在制作一个简单的构建系统，它可以检测源代码中的每个 javascript 文件，以进行混淆和缩小。

## 先决条件

要阅读本教程，你需要 Javascript 和 Nodejs 的基础知识。我们将使用 javascript-obfuscator 来混淆和缩小代码。

#### 走吧...

### 安装

我们将只使用一个外部库 [javascript-obfuscator](https://github.com/javascript-obfuscator/javascript-obfuscator) ，它可以作为开发依赖项安装。

`npm install --save-dev javascript-obfuscator`

### 导入&文件夹

我们总共需要三个库——fs、path 和 javascript-obfuscator。

```
const Fs = require('fs');
const Path = require('path');
const JavaScriptObfuscator = require('javascript-obfuscator'); 
```

源代码将保存在 *src* 文件夹中，最终构建文件将在 *build* 文件夹中生成。

```
const src = Path.join(__dirname, '/src/');
const build = Path.join(__dirname, '/build/'); 
```

### 读取目录

所有的目录都将被递归读取，并找到 javascript 文件，这些文件将被混淆和缩小。为了读取文件，我们将使用 fs 模块。

```
function readDirectory(dirPath)
{
    Fs.readdir(dirPath, (err, files) => 
    {
        if(err)
        {
            console.error("Could not list directory.", err);
            process.exit(1);
        }

        files.forEach((file, index) => // loop through every file
        {
            let path = Path.join(dirPath, file);
            console.log(path);
        });
    });
} 
```

这将给我们提供目录中每个文件和文件夹的路径。但是我们需要区分文件和目录，并进一步读取找到的目录。这可以通过 fs 提供的`stat`函数来完成。

```
Fs.stat(path, (err, stat) => 
{
    if(err)
    {
        console.log("error in stating file.", err);
        return;
    }

    if(stat.isFile())
    {
        console.log(`${path} is a file`);
    }
    else if(stat.isDirectory())
    {
        console.log(`${path} is a folder`);
        readDirectory(path); // Further read the folder.
    }
}); 
```

### 从 src 复制文件和目录到构建目录

现在是我们开始复制在 src 中找到的所有文件来构建目录的最佳时机。在复制的同时，我们还将创建 src 中不存在的目录。

```
if(stat.isFile())
{
    const newPath = path.replace(src, build); // Replace src path with build path.
    Fs.copyFileSync(path, newPath); // Copy file from old path in src to new path in build.
    if(newPath.endsWith(".js")) // Check if it is javascript file.
    {
        obfuscate(newPath); // Obfuscate copied file in build folder.
    }
}
else if(stat.isDirectory())
{
    var newDir = path.replace(src, build); // Replace src path with build path.
    if (!Fs.existsSync(newDir)) // Check if directory exists or not.
    {
        Fs.mkdirSync(newDir); // Create new directory.
    }
    readDirectory(path); // Further read the folder.
} 
```

### 混淆和缩小

最后，找到的 javascript 文件将被混淆和缩小。为此，我们将使用`JavaScriptObfuscator.obfuscate`函数，它将代码作为第一个参数，将配置对象作为第二个参数。

```
function obfuscate(filePath)
{
    const content = Fs.readFileSync(filePath).toString(); // Read the files content.
    var result = JavaScriptObfuscator.obfuscate(content,
        { // Config for obfuscation
            compact: true, // Set true to enable minification
            controlFlowFlattening: true,
            target: 'browser'
        }
    ); // Generated minified and obfuscated code

    Fs.writeFileSync(filePath, result.getObfuscatedCode()); // Write obfuscted and minified code generated back to file.
} 
```

你可以在这里阅读更多关于配置选项[的信息。](https://github.com/javascript-obfuscator/javascript-obfuscator/#javascript-obfuscator-options)

## 终于...

这里是完整的代码

```
const Fs = require('fs');
const Path = require('path');
const JavaScriptObfuscator = require('javascript-obfuscator');

const src = Path.join(__dirname, '/src/');
const build = Path.join(__dirname, '/build/');

readDirectory(src); // Start reading with src directory.

function readDirectory(dirPath)
{
    Fs.readdir(dirPath, (err, files) => 
    {
        if(err)
        {
            console.error("Could not list directory.", err);
            process.exit(1);
        }

        files.forEach((file, index) => // loop through every file
        {
            let path = Path.join(dirPath, file);

            Fs.stat(path, (err, stat) => 
            {
                if(err)
                {
                    console.log("error in stating file.", err);
                    return;
                }

                if(stat.isFile())
                {
                    const newPath = path.replace(src, build); // Replace src path with build path.
                    Fs.copyFileSync(path, newPath); // Copy file from old path in src to new path in build.
                    if(newPath.endsWith(".js")) // Check if it is javascript file.
                    {
                        obfuscate(newPath); // Obfuscate copied file in build folder.
                    }
                }
                else if(stat.isDirectory())
                {
                    var newDir = path.replace(src, build); // Replace src path with build path.
                    if (!Fs.existsSync(newDir)) // Check if directory exists or not.
                    {
                        Fs.mkdirSync(newDir); // Create new directory.
                    }
                    readDirectory(path); // Further read the folder.
                }
            });         
        });
    });
}

function obfuscate(filePath)
{
    const content = Fs.readFileSync(filePath).toString(); // Read the files content.
    var result = JavaScriptObfuscator.obfuscate(content,
        { // Config for obfuscation
            compact: true, // Set true to enable minification
            controlFlowFlattening: true,
            target: 'browser'
        }
    ); // Generated minified and obfuscated code

    Fs.writeFileSync(filePath, result.getObfuscatedCode()); // Write obfuscted and minified code generated back to file.
} 
```

**万岁！！！你坚持到最后。**
希望你会觉得有用。如果是的话，请通过评论和分享来表达你的爱。

##### 感谢阅读:)