# CLI 启动程序说你好，世界！

> 原文：<https://dev.to/drozerah/cli-starter-says-hello-world-42g3>

对社区来说，

你好。这条短信让你知道样板文件在这个阳光灿烂的日子诞生了！

本资料附带了在 Node.js 和 NPM 环境中开始创建新的 CLI 实用程序所需的最低设置和文件...

试试看！

**结构**

```
├───bin/
│   └───cli.js
├───.gitignore
├───licence
├───package-lock.json
├───package.json
└───README.md 
```

**安装**

```
$ git clone https://github.com/Drozerah/cli-starter.git 
```

然后全局安装:

```
$ npm install -g 
```

完成此步骤后，您需要:

```
$ npm link 
```

这个命令将在本地注册您的 CLI 项目，因此您现在可以在终端的任何地方运行下面的命令:

```
$ cli-starter 
```

该命令将在终端中提示一条消息，表示安装 *[cli-starter](https://github.com/Drozerah/cli-starter)* 一切正常:

[![cli starter](img/6a8d2928f6515d143a80f3fd0426b57e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JkOphTbr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/Drozerah/MyGitHubStorage/master/gif/cli-starter/cli-starter.gif)

就是这样！

下一步？

叉它&有创意！

感谢阅读！

下次见！

德罗泽拉