# 在 create-react-app 项目中包含不同的存储库

> 原文：<https://dev.to/vittoriozamboni/include-different-repositories-in-a-create-react-app-project-207d>

*封面图片:爱尔兰多尼戈尔郡本贝格海滩*

当我开始编写 React 应用程序时，所有的代码库都在一个存储库中。没有代码共享，没有上下文分离。

一旦我对探索新的解决方案感兴趣，我就想在家里建立一个小仪表板作为测试新库、React hooks 或与 Vue 等其他框架集成的平台。

创建 Dashboard 主干是一个简单的操作:脚手架的几个组件、一个基本的身份验证模块、一个带有几个开关用例的 Routes 组件和一个包含不同项目的`apps`文件夹。

我来自 Python/Django 背景，我想在不同的存储库中组织我的代码，就像我在以前的项目中所做的那样。不幸的是，这个操作并不像预期的那样简单。

在 Python 中，有一些我熟悉的工具帮助我管理这个需求:`virtualenvwrapper`就是其中之一。
它的功能之一(`add2virtualenv`)是能够在相同的环境下将不同的存储库链接在一起，并且仍然能够修改它们，而无需任何重新安装或部署——另一个选项是来自存储库文件夹的`pip install -e`。

可惜和 npm/yarn 还有`create-react-app`不一样；它们都允许`link`，但是每个存储库必须解决自己的依赖关系并安装它们。

## 项目结构

当前的项目结构基于标准的`create-react-app`示例。

```
package.json
src/
    apps/
        ...
    libs/
        ...
    scaffold/
        ...
    App.js
    index.js 
```

Enter fullscreen mode Exit fullscreen mode

我的想法是将`libs`文件夹中的代码共享给所有的应用程序，并将每个应用程序保存在自己的库中。

我第一次尝试的是`yarn link`，但是效果不好。这种方法假设应用程序内部的代码已经打包，并且它自己的依赖项已经解决。

有东西不见了！

## 避免弹射

我做的第一件事是去`eject`探索配置机会。
如果可以恢复的话，这实际上是测试配置的一个好方法(如果你在一个版本控制的文件夹下，这是非常容易的)。

然而，弹出(并保留每个配置文件)并不是我的首选解决方案，因为当您长时间手动操作并开始定制许多东西时，您就不容易返回，并且您需要一个接一个地维护依赖关系。

另一方面，`create-react-app`不允许您修改配置文件，以保持项目尽可能的通用和标准。

网上几乎没有提出解决方案:切换到 razzle.js，使用 next.js，用 lerna(monorepo 方法——只有一个所有者)或 fork `create-react-app`重新思考项目结构。

为了保持项目简单，我不想介绍 next.js 或 razzle.js。我希望在其他项目中重用我的仪表板代码，使用框架可能不是最好的解决方案。

## 叉

在所有解决方案中，我选择了分叉`create-react-app`库，遵循[本指南](https://medium.com/@denis.zhbankov/maintaining-a-fork-of-create-react-app-as-an-alternative-to-ejecting-c555e8eb2b63)。

在`react-scripts`包中，我在`config/webpack.config.js`中添加了以下几行。

*   将内容保存到变量中，而不是直接返回配置:

```
// remove the return statement and save the content
-  return { +  let config = {
     mode: isEnvProduction ? 'production' : isEnvDevelopment &&  'development', 
```

Enter fullscreen mode Exit fullscreen mode

*   检查名为`customWebpack.config.js`的文件是否存在于项目根文件夹中，如果它有一个`modify`函数，用函数的结果覆盖配置:

```
 // our own hints via the FileSizeReporter
     performance: false,
   };
+  console.log('Checking for custom webpack config');
+  if (fs.existsSync('./customWebpack.config.js')) {
+    console.log('  -- custom config found!');
+    const customWebpackConfig = require(path.resolve(
+      __dirname,
+      '../../../../customWebpack.config'
+    ));
+    if (customWebpackConfig.modify) {
+      config = customWebpackConfig.modify(config, { webpackEnv });
+    }
+  }
+
+  return config; }; 
```

Enter fullscreen mode Exit fullscreen mode

这种类似的方法被`razzle`使用(以一种更奇特的方式)。

在我的项目中，我必须做三件事:

*   添加一个`customWebpack.config.js`文件:

```
module.exports = {
    modify: (config, { webpackEnv }) => {

        // List of external repositories that have to be added
        // to the testers to being correctly processed 
        let externalRepositories = [];
        if (process.env.REACT_APP_EXTERNAL_REPOSITORIES) {
            externalRepositories = process.env.REACT_APP_EXTERNAL_REPOSITORIES.split(',');
        }

        // Set a list of repositories required for this project
        const projectRepositories = [ 
            'my-test-repo'           
        ];

        // Validate that all repositories have been set before starting
        projectRepositories.forEach(repo => {
        if (externalRepositories.filter(eRepo => eRepo.endsWith(repo)).length !== 1)
            throw new Error(`==> Repository ${repo} must be included in ` +
                `.env.local REACT_APP_EXTERNAL_REPOSITORIES variable`);
        });

        config.module.rules[2].oneOf.forEach((test, index) => {
        if (test.include) {
            config.module.rules[2].oneOf[index].include = [
            ...(Array.isArray(test.include) ? test.include : [test.include]),
            ...externalRepositories,
            ];
        }
        });
    }
    return config;
}; 
```

Enter fullscreen mode Exit fullscreen mode

*   将存储库添加到. env.local 文件中的`REACT_APP_EXTERNAL_REPOSITORIES`:

```
REACT_APP_EXTERNAL_REPOSITORIES=~/repositories/my-test-repo 
```

Enter fullscreen mode Exit fullscreen mode

*   最终创造了一个链接

```
ln -s ~/repositories/my-test-repo dashboard-ui/src/apps/ 
```

Enter fullscreen mode Exit fullscreen mode

## 最后的考虑

这种方法不是 JS/React 应用程序/模块开发中的标准方法，但它让我拥有了开发 python 应用程序时所拥有的优势:

*   每个模块都在它自己的库中:不同的库可能有不同的可见性、权限，遵循不同的合并策略，并且有它自己的 wiki(或者甚至不同的团队)；
*   CRA 可以立即获得更改，而无需额外的编译步骤；
*   应用程序是从一个单点捆绑的(然而，这也是 CI/CD 方面的一个缺点，但是`node_modules`可以被缓存以加速构建操作)。

也有一些缺点，但对我的管道来说，它们不是问题:

*   如前所述，单个存储库中的一个更改需要再次捆绑完整的应用程序。可以采用混合的方法，只在开发中添加外部库，利用`webpackEnv`变量，用它们自己的管道构建单个模块；
*   新存储库的设置并不简单:我们需要创建一个链接并将存储库添加到一个 env 变量中(这也可能是自动的)，并且我们需要这样做以便进行构建。

你能找到更简单的方法来达到这个结果吗？你认为这是一个可怕的方法？请分享你的看法！