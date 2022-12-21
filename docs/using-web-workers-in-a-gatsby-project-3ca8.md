# 在 Gatsby 项目中使用 Web Workers

> 原文：<https://dev.to/evanwinter/using-web-workers-in-a-gatsby-project-3ca8>

【2022 年 2 月更新——这可能不再管用了...我已经很久没参与盖茨比的项目了。我会试着找些时间重温这个，让它再次工作。

## 简介

有一个 CPU 密集型任务阻塞了您的用户界面？你想用一个 [Web Worker](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers) 把它卸载到一个后台线程吗？下面是如何在一个 Gatsby 项目的背景下完成的。

*我在 [GitHub 的一期](https://github.com/gatsbyjs/gatsby/issues/12095)中偶然发现了这种方法，并决定在这里写下来，供其他寻求相同解决方案的人参考。*

## 安装依赖项

安装[工作装载机](https://github.com/developit/workerize-loader) :

```
npm install --save workerize-loader # or yarn add workerize-loader 
```

Enter fullscreen mode Exit fullscreen mode

## 将 workerize-loader 添加到 Webpack 包

现在，我们需要将我们的`workerize-loader`配置注入到 Gatsby 生成的 Webpack 包中。

为此，将以下代码添加到`gatsby-node.js` :

```
exports.onCreateWebpackConfig = ({ actions: { replaceWebpackConfig }, getConfig }) => {
  const config = getConfig()

  config.module.rules.push({
    test: /\.worker\.js$/,
    use: { loader: 'workerize-loader' }
  })

  config.output.globalObject = 'this'

  replaceWebpackConfig(config)
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码获取了 Gatsby 为我们生成的当前 Webpack 配置，并添加了 workerize-loader 规则，该规则查找带有`.worker`后缀的文件，并将它们作为 Web Workers 进行处理。

## 创建职工档案

创建一个带有`.worker`后缀的文件，比如:

```
// expensive.worker.js
export function doExpensiveTask(num) {
  let result = 0
  for (let i = 0; i < Math.pow(num, 10); i++) {
    result += result / i
  }
  return result
} 
```

Enter fullscreen mode Exit fullscreen mode

## 用法

现在，您可以导入它并像普通函数一样使用它。

```
import MyWorker from "path/to/expensive.worker.js"

const result = MyWorker.doExpensiveTask(5) 
```

Enter fullscreen mode Exit fullscreen mode

## 奖励:异步示例

```
// expensive-async.worker.js
export async function doExpensiveAsyncTask() {
  const url = "https://jsonplaceholder.typicode.com/todos/1"
  return await fetch(url)
} 
```

Enter fullscreen mode Exit fullscreen mode

```
import MyAsyncWorker from "path/to/expensive-async.worker.js"

const result = await MyAsyncWorker.doExpensiveAsyncTask() 
```

Enter fullscreen mode Exit fullscreen mode