# 在机器上测试 Circle CI config.yml

> 原文：<https://dev.to/0xkoji/test-circle-ci-config-yml-on-your-machine-26f6>

团队使用了 Circle CI，我只是用它来运行测试，这意味着我还没有碰到`config.yml`幸运的是哈哈

然而，对于当前的项目，我需要建立一个新的 Circle CI 环境。(实际上，我的 config.yml 并不复杂，因为我已经在 Docker 映像上完成了大部分工作😂)

然后，我注意到 Circle CI 有一个 CLI 工具在本地测试 config.yml。
[https://circleci.com/docs/2.0/local-cli/](https://circleci.com/docs/2.0/local-cli/)

该工具有两个功能。一个是验证`config.yml`，另一个是运行作业。要求是安装 Docker。
这个工具正在使用自己的 docker 映像。

```
circleci/picard             latest              7d0931871af3        8 days ago          103MB 
```

Enter fullscreen mode Exit fullscreen mode

```
# validate 
$ circleci config validate

# run
$ circleci local execute --job build 
```

Enter fullscreen mode Exit fullscreen mode

执行速度不是很快，但我认为这个工具还是有用的，有时我们需要等待别人的任务。这可以节省时间，并帮助我们在 Circle CI 仪表板上看到错误。

~~不过我还在纠结圈 CI lol
[https://discuse . Circle CI . com/t/trying-to-build-node-with-type script-but-Circle-CI-references-to-react/31546](https://discuss.circleci.com/t/trying-to-build-node-with-typescript-but-circle-ci-mentions-to-react/31546)~~

~~如果你知道解决方法，请告诉我！~~