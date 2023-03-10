# Ember App 升级容易吗？

> 原文：<https://dev.to/rakchamp/is-ember-app-upgrade-easy-38b3>

当然，是的，用几个命令就可以轻松实现。

# 期待什么

升级 ember 应用程序这取决于应用程序是否有补丁，次要和主要版本更新。

*   补丁更改->从 3.4.1 到 3.4.3
*   小改动-> 3.4 到 3.5
*   主要变化->从 3.0 到 4.0

### 补丁变更

主要是在补丁版本更新中，引入了内部重构和 bug 修复，以及 ember 中依赖版本的变化。

### 微小的变化

在小版本更新中，引入了新的特性，开发者应该注意到反对意见。在微小的变化，烬核心小组将不会完全删除不赞成的变化。它将在控制台中以不赞成警告的形式登录，将来可能会被完全删除。

### 重大变化

在重大变更更新中，引入了突破性的更改，因此开发人员应该在升级应用程序之前确保弃用。

## 如何升级

正如我所说的，这很容易做到以下几点

*   步骤 1:全局安装 npm 包 [ember-cli-update](https://www.npmjs.com/package/ember-cli-update) 。
*   步骤 2:运行以下命令，它使用当前应用程序为最新版本的执行 ember 初始化作业。

```
ember-cli-update --resolve-conflicts 
```

*   第三步:主要是在主要版本中，代码更改非常困难。但没必要担心。烬解决它在单一的命令。运行以下命令，

```
ember-cli-update --run-codemods 
```

上述命令将列出类似下图的代码模块

[![](img/00f7f3e583046b6ac4f1ed026cb08943.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b6iqJlsB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7g075jvpdx9hjeizvfkm.png)

#### 那么什么是 codemods 呢？

Codemod 是一个工具/库，帮助您进行大规模的代码库重构，这些重构可以部分自动化，但仍然需要人工监督和偶尔的干预。

通过运行 codemod，它覆盖了 ember 应用程序中的所有内容。

很酷吗？

如果要更新特定版本，请运行以下命令。

```
ember-cli-update --to 3.4.3 
```

## 故障排除

如果您想要恢复更改，请运行以下命令，这将使您回到更新之前。

```
git reset --hard
git clean -f 
```

### ember-CLI-update 命令的选项:

[![](img/e1321056a391f68ca938349b80655e1f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YSpRsY-Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m8rotm0u7lxf93aeexdy.png)

准备好启动 ember 版本升级了吗？

希望你搞清楚这个概念。感谢阅读。