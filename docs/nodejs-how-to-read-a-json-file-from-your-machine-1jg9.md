# 如何从你的机器上读取一个 json 文件

> 原文：<https://dev.to/miku86/nodejs-how-to-read-a-json-file-from-your-machine-1jg9>

## [T1】简介](#intro)

所以我们[在我们的机器上安装了 NodeJS](https://dev.to/miku86/what-is-nodejs-how-to-install-it-1900) 。

现在我们想学习如何使用[文件系统(FS)模块](https://nodejs.org/api/fs.html)从我们的机器中读取 JSON 文件。

## 用一些数据创建一个文件

*   打开你的终端
*   创建一个名为`data-read.json`的文件:

```
touch data-read.json 
```

Enter fullscreen mode Exit fullscreen mode

*   向其中添加一些 JSON 数据:

```
[{  "id":  1,  "name":  "miku86"  }] 
```

Enter fullscreen mode Exit fullscreen mode

## 写一个简单的脚本

*   打开你的终端
*   创建一个名为`index.js`的文件:

```
touch index.js 
```

Enter fullscreen mode Exit fullscreen mode

*   将这段 JavaScript 代码添加到其中:

```
const fs = require('fs');

const FILE_NAME = 'data-read.json';

const readFileAsync = () => {
  fs.readFile(FILE_NAME, (error, data) => {
    console.log('Async Read: starting...');
    if (error) {
      console.log('Async Read: NOT successful!');
      console.log(error);
    } else {
      try {
        const dataJson = JSON.parse(data);
        console.log('Async Read: successful!');
        console.log(dataJson);
      } catch (error) {
        console.log(error);
      }
    }
  });
};

readFileAsync(); 
```

Enter fullscreen mode Exit fullscreen mode

注意:我们使用异步 [`readFile`](https://nodejs.org/api/fs.html#fs_fs_readfile_path_options_callback) 函数来读取数据，因为我们不想阻塞其他任务。您也可以使用 [`readFileSync`](https://nodejs.org/api/fs.html#fs_fs_readfilesync_path_options) 来同步读取数据，但是这可能会阻塞一些其他任务。

注意:你可以用文件系统模块做很多事情，因此[读取 FS 模块](https://nodejs.org/api/fs.html#fs_file_system_flags)的文档。

* * *

## 每行解释

```
// import the file system module
const fs = require('fs');

// save the file name of our data in a variable (increase readability)
const FILE_NAME = 'data-read.json';

const readFileAsync = () => {
  // run async function to read file
  fs.readFile(FILE_NAME, (error, data) => {
    console.log('Async Read: starting...');

    if (error) {
      // if there is an error, print it
      console.log('Async Read: NOT successful!');
      console.log(error);
    } else {
      try {
        // try to parse the JSON data
        const dataJson = JSON.parse(data);
        console.log('Async Read: successful!');
        console.log(dataJson);
      } catch (error) {
        // else print an error (e.g. JSON was invalid)
        console.log(error);
      }
    }
  });
};

// run the function
readFileAsync(); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 从终端运行它

*   运行它:

```
node index.js 
```

Enter fullscreen mode Exit fullscreen mode

*   结果:

```
Async Read: starting...
Async Read: successful!
[ { id: 1, name: 'miku86' } ] 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 进一步阅读

*   [文件系统模块](https://nodejs.org/api/fs.html)
*   [文件系统标志](https://nodejs.org/api/fs.html#fs_file_system_flags)

* * *

## 提问

*   你曾经使用过 [`fs Promises API`](https://nodejs.org/api/fs.html#fs_fs_promises_api) 这种用承诺代替回调的方式吗？