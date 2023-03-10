# node.js 如何将数据作为 json 写入您的机器

> 原文：<https://dev.to/miku86/nodejs-how-to-write-data-as-json-to-your-machine-1fab>

## [T1】简介](#intro)

所以我们[在我们的机器上安装了 NodeJS](https://dev.to/miku86/what-is-nodejs-how-to-install-it-1900) 。

上次，我们学习了如何从我们的机器中读取数据。

现在我们想学习如何使用[文件系统(FS)模块](https://nodejs.org/api/fs.html)将数据作为 JSON 写入我们的机器。

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

const FILE_NAME = 'data-write.json';
const NEW_DATA = [{ id: 2, name: 'Max' }];

const writeFileAsync = (newData) => {
  const stringifiedData = JSON.stringify(newData);

  fs.writeFile(FILE_NAME, stringifiedData, (error) => {
    if (error) {
      console.log('Async Write: NOT successful!');
      console.log(error);
    } else {
      console.log('Async Write: successful!');
      console.log(stringifiedData);
    }
  });
};

writeFileAsync(NEW_DATA); 
```

Enter fullscreen mode Exit fullscreen mode

注意:我们使用异步 [`writeFile`](https://nodejs.org/api/fs.html#fs_fs_writefile_file_data_options_callback) 函数来写数据，因为我们不想阻塞其他任务。您也可以使用 [`writeFileSync`](https://nodejs.org/api/fs.html#fs_fs_writefilesync_file_data_options) 同步写入数据，但这可能会阻塞其他一些任务。

注意:你可以用文件系统模块做很多事情，因此[读取 FS 模块](https://nodejs.org/api/fs.html#fs_file_system_flags)的文档。

* * *

## 每行解释

```
// import the file system module
const fs = require('fs');

// save the file name of our data in a variable (increase readability)
const FILE_NAME = 'data-write.json';

// save the new data in a variable (increase readability)
const NEW_DATA = [{ id: 2, name: 'Max' }];

const writeFileAsync = (newData) => {
  // convert the  JavaScript values to a JSON string
  const stringifiedData = JSON.stringify(newData);

  // run async function to write file
  fs.writeFile(FILE_NAME, stringifiedData, (error) => {
    if (error) {
      // if there is an error, print it
      console.log('Async Write: NOT successful!');
      console.log(error);
    } else {
      console.log('Async Write: successful!');
      console.log(stringifiedData);
    }
  });
};

// run the function
writeFileAsync(NEW_DATA); 
```

Enter fullscreen mode Exit fullscreen mode

注意:我们使用 [`JSON.stringify()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 将 JavaScript 值转换成 JSON 字符串。

* * *

## 从终端运行它

*   运行它:

```
node index.js 
```

Enter fullscreen mode Exit fullscreen mode

*   结果:

```
Async Write: successful!
[{"id":2,"name":"Max"}] 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 下一步

*   问:当`data-write.json`已经存在时会发生什么？
*   问:我们如何解决这个问题？
*   问:我们需要一些额外的错误处理吗？(= >文档)

* * *

## 进一步阅读

*   [文件系统模块](https://nodejs.org/api/fs.html)
*   [文件系统标志](https://nodejs.org/api/fs.html#fs_file_system_flags)

* * *

## 提问

*   你曾经使用过 [`fs Promises API`](https://nodejs.org/api/fs.html#fs_fs_promises_api) 这种用承诺代替回调的方式吗？