# how to:Jenkins 中值的下拉列表

> 原文：<https://dev.to/malexe/howto-drop-down-list-for-values-2aop>

如何在 Jenkins 中创建带有预定义项目的下拉列表？

先决条件:

*   [扩展选择参数插件](https://wiki.jenkins.io/display/JENKINS/Extended+Choice+Parameter+plugin)

步骤 1:在全局属性中创建位于[http://jenkins.domain.org/configure](http://jenkins.domain.org/configure)的属性，并命名为项目和以逗号分隔的项目列表

第二步:在作业配置中添加参数*扩展选择参数*
为您的参数设置名称(例如*JIRA _ 项目* )
选择 *Groovy 脚本*并添加下一个 Groovy 代码

```
 import jenkins.model.*
    nodes = Jenkins.instance.globalNodeProperties
    nodes.getAll(hudson.slaves.EnvironmentVariablesNodeProperty.class)
    envVars = nodes[0].envVars
    return "${envVars['PROJECTS']}".split(',') 
```

第三步:享受！