# 自动化您的节点依赖关系更新

> 原文：<https://dev.to/dennismphil/automate-your-node-dependency-updates-4aga>

一个相当大的节点应用程序将有 100 个依赖项。保持它们全部更新是开发人员在某个时候需要执行的🧹chore。要么您忽略依赖项更新，直到您被一组非常旧的依赖项所困，这些依赖项阻碍了您处理🦹‍♂️安全漏洞的进度，要么您将宝贵的应用程序开发时间花费在以合理的节奏手动测试更新上。

让我们看看如何在企业环境中自动化这个过程，假设您有某种 CI/CD 环境和一个私有 GitHub repo。

## 🥒佐料

1.  对您的代码进行可靠的单元测试。如果您对 UI 组件进行端到端测试和快照测试，将会获得额外的奖励
2.  `next-update` *一个 npm 包，测试您的依赖项是否可以在不中断测试的情况下更新。*
3.  这是一个来自 GitHub”的命令行应用程序，可以与您的 GitHub repo 进行交互。`hub`与`git` CLI 完全相似，是一个嵌入式替代品，但增加了与 GitHub 交互的功能。方便在更新操作后打开一个拉取请求。

## 📝秘诀

*   `npm install next-update --save-dev`
    *将下一次更新安装为开发依赖项。*

*   在您的`package.json`脚本部分
    中配置一个`npm`脚本`dep:update`

    ```
    // package.json
    {
      "name": "a-sample-node-project",
      "version": "0.0.1",
      "description": "A sample node project",
      "scripts": {
        "test": "jest",
        "start": "node app.js",
        "dep:update": "next-update" // Configure an npm script
      },
      "devDependencies": {
        "next-update": "^3.6.0"
      },
      "dependencies": {
      },
    } 
    ```

*   `npm run dep:update`
    *运行脚本。`next-update`将继续查找所有新包。按顺序更新它们，如果测试通过，就保持更新。*

*   下载并安装`hub` cli

    ```
    # download-hub.sh
    HUB_CLI=/opt/hub-linux/bin/hub

    if [[ ! -f $HUB_CLI ]]; then wget https://github.com/github/hub/releases/download/v2.12.2/hub-linux-amd64-2.12.2.tgz 
       tar zxvf hub-linux-amd64-2.12.2.tgz 
       rm -rf hub-linux-amd64-2.12.2.tgz /opt/hub-linux
       mv hub-linux-amd64-2.12.2 /opt/hub-linux
    fi 
    ```

*   配置集线器

    ```
    git config --global --replace-all hub.host github.yourdomain.com
    git config --global --replace-all hub.protocol git 
    ```

*   指示🤖打开拉取请求的机器人

    ```
    $HUB_CLI add package.json package-lock.json
    $HUB_CLI commit -m "🤖 [BOT] Automated dependency update"
    $HUB_CLI pull-request \
        --push \
        -m "Pull Request Subject" \
        -m "Pull Request Description" \
        --no-edit \
        --reviewer user-id1,user-id2 
    ```

*   将该脚本挂接到您的 CI/CD 环境中，以便每天运行

[![](img/63f80bbc673910b9b35f6ab7e6eee52e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MSWY6UOK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3re3uynhyg0kc4jpw7hx.png)

## 包装完毕

我们看到了如何检查和更新节点依赖关系，以及如何自动化打开 PR 的过程。如果你的项目是开源的，你可以使用像 Greenkeeper 这样的服务。

干杯

[![](img/e8a4102ace377c2d31a750d203fe8122.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R-f1g29e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/src9sniteiq1jdvbxg1z.png)

请查找我以前在媒体上的文章:

1.  [使用 SVG 的单边圆角矩形](https://medium.com/@dennismphil/one-side-rounded-rectangle-using-svg-fb31cf318d90)
2.  [Visual Studio 代码—调试模式](https://medium.com/@dennismphil/debugging-node-js-using-visual-studio-code-c30e5eeb9e38)
3.  我希望 TypeScript 成功。
4.  我的笔记本电脑里有什么？
5.  [CoffeeScript 最受欢迎的特性即将在 JavaScript 中出现](https://medium.com/@dennismphil/coffeescripts-most-loved-feature-soon-in-javascript-b2f6740c2dc7)