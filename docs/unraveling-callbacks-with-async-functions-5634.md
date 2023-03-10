# 用异步函数解开回调

> 原文：<https://dev.to/ivanalejandro0/unraveling-callbacks-with-async-functions-5634>

## 要求

我假设您熟悉 Javascript 和这些概念:

*   [箭头功能](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
*   [承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises)
*   [异步功能](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)

## 例题和问题

这是一个移动文件的函数的真实例子。这是我们的一个移动应用的一部分。

代码并不复杂，但是很难一眼读懂；感觉很糟糕。所以我试着对它进行了一点重构，看看能否提高它的可读性。

```
import path from 'path';

/**
 * Moves a file from one directory to another.
 *
 * @param { String } basePath: the base path for both relativeSourcePath
 *                             and relativeDestinationPath.
 * @param { String } relativeSourcePath: the relative path of the file.
 * @param { String } relativeDestinationPath: the relative new path of the file.
 *
 * @return { Promise } resolves with no value if the file is
 *                     successfully moved.
 */
function move(basePath, relativeSourcePath, relativeDestinationPath) {
  return new Promise((resolve, reject) => {
    const destinationPath = path.dirname(relativeDestinationPath);
    const filename = path.basename(relativeDestinationPath);

    ensureDirectory(basePath, destinationPath).then(() => {
      window.resolveLocalFileSystemURL(basePath, baseDirEntry => {
        baseDirEntry.getFile(relativeSourcePath, {}, sourceFileEntry => {
          baseDirEntry.getDirectory(destinationPath, {}, destDirEntry => {
            sourceFileEntry.moveTo(destDirEntry, filename, resolve, reject);
          }, error => {
            console.error('[move] Error getting destination directory', error);
            reject(error);
          });
        }, error => {
          console.error('[move] Error getting source file', error);
          reject(error);
        });
      });
    }).catch(error => reject(error));
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的问题主要是我们有一个深度嵌套的代码，这使得推理、维护和调试更加困难。

## 战略

为了理解发生了什么，我试图做的是在视觉上隔离回调，识别我们从每个调用中提取的相关数据，以及我们在哪里使用它。

之后，我包装了`await`和`Promise`上的函数来模拟一个返回值的常规函数。

让我们看看如何从基于回调的函数到异步函数。

```
// you call this `doStuff` function to do something and you get `data` if it
// succeeds or an `error` if it fails.
doStuff(param1, param2,
    data => {
      /* do something with the data */
    },
    error => {
      /* problem with doStuff */
    }
  );

// We can extract our functions to handle success and failure like so:
const onSuccess = data => { /* do something with the data */ }
const onFailure = error => { /* problem with doStuff */ }

doStuff(param1, param2, onSuccess, onFailure); 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们用一个`Promise`来包装我们的调用，用`await`来包装它的结果。

```
try {
  const data = await new Promise((resolve, reject) => {
    const onSuccess = data => resolve(data);
    const onFailure = error => reject(error);
    doStuff(param1, param2, onSuccess, onFailure);

    // we don't really need a separate definition for the functions
    // we can inline them like so:
    doStuff(param1, param2, data => resolve(data), error => reject(error));
  });

  /* do something with the data */
} catch(error) {
  /* problem with doStuff */
} 
```

Enter fullscreen mode Exit fullscreen mode

或者作为一个衬里。

```
try {
  const data = await new Promise((resolve, reject) => doStuff(param1, param2, data => resolve(data), error => reject(error)));
  /* do something with the data */
} catch(error) {
  /* problem with doStuff */
} 
```

Enter fullscreen mode Exit fullscreen mode

成功/失败处理程序有点多余，所以让我们简化一下。

```
try {
  const data = await new Promise((resolve, reject) => doStuff(param1, param2, resolve, reject));
  /* do something with the data */
} catch(error) {
  /* problem with doStuff */
} 
```

Enter fullscreen mode Exit fullscreen mode

就这样，我们的最终形状。这看起来没有太大的变化，但这允许我们有一个更浅的代码，而不是一个真正嵌套的回调集。

## 最终结果

下面是我们的函数使用上述策略重构后的样子。

```
import path from 'path';

/**
 * Moves a file from one directory to another.
 *
 * @param { String } basePath: the base path for both relativeSourcePath
 *                             and relativeDestinationPath.
 * @param { String } relativeSourcePath: the relative path of the file.
 * @param { String } relativeDestinationPath: the relative new path of the file.
 *
 * @throws { Error } if there was a problem moving the file.
 */
async function move(basePath, relativeSourcePath, relativeDestinationPath) {
  const destinationPath = path.dirname(relativeDestinationPath);
  const filename = path.basename(relativeDestinationPath);

  try {
    await ensureDirectory(basePath, destinationPath)

    const baseDirEntry = await new Promise(resolve =>
      window.resolveLocalFileSystemURL(basePath, resolve)
    );

    const sourceFileEntry = await new Promise((resolve, reject) =>
      baseDirEntry.getFile(relativeSourcePath, {}, resolve, reject)
    );

    const destDirEntry = await new Promise((resolve, reject) =>
      baseDirEntry.getDirectory(destinationPath, {}, resolve, reject)
    );

    await new Promise((resolve, reject) =>
      sourceFileEntry.moveTo(destDirEntry, filename, resolve, reject)
    );
  } catch (error) {
    // here you can do something about this problem
    console.error('There was a problem moving the file.', error);
    throw error;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

对于这个特殊的例子，记录两个不同的错误没有太大的意义，所以我将所有的调用包装在一个`try` / `catch`中，只记录那里的问题。

您的用例可能会有所不同，您可能希望有多个处理块或根本没有处理块，并记录您的函数可能会出现不同的错误。如果您不想在发生错误时对该函数执行特定的操作，而是将它留给调用者，那么这是非常有用的。

## 最后一句话

只需做一点工作，我们的代码现在更容易阅读和维护。

这个问题很常见，通常被称为“回调地狱”，正如你在这里看到的:[http://callbackhell.com/](http://callbackhell.com/)

我希望这篇文章能给你一些想法，让你的生活更轻松。

## 免责声明

我为 SpiderOak 工程博客写了这篇文章，它于 2019 年 7 月 10 日发表。
[https://engineering . spider oak . com/unraveling-callbacks-with-async-functions/](https://engineering.spideroak.com/unraveling-callbacks-with-async-functions/)

原帖授权为: [Creative Commons BY-NC-ND](https://creativecommons.org/licenses/by-nc-nd/4.0/)