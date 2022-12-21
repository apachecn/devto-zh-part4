# 从吉拉迁移到 Azure DevOps

> 原文：<https://dev.to/sogeti/migrating-from-jira-to-azure-devops-2aed>

就我而言，一个团队可以使用任何他们想要的工具来跟踪他们的工作项目和计划。但是，我个人最喜欢的是蓝色 DevOps。目前，我正在帮助一些团队将他们的吉拉项目迁移到 DevOps，这篇博客解释了你如何也能做到这一点！

## 整齐划一，标准一致

由于几个原因，如果团队为同一目的使用多种工具会令人沮丧。为了统一起见，我认为在你的公司内部最好有标准。就我目前的客户而言，这个标准就是 Azure DevOps。

显然，您希望将您的工作从旧平台迁移到新平台。Azure DevOps 能够使用 API 接收数据，您也可以导出吉拉。要手动做到这一点，并找出所有的移动部分，你会有相当大的挑战。

## 输入固化

[Solidify](https://www.solidify.se/) 的团队给我介绍了一个非常棒的工具，即[吉拉到 Azure DevOps 的工作项迁移工具](https://github.com/solidify/jira-azuredevops-migrator)。

> 吉拉到 Azure DevOps 工作项迁移工具允许您从吉拉导出数据，并将其作为工作项导入 Azure DevOps 或 Microsoft Team Foundation Server

这听起来正是我们所需要的！因为每个项目都是不同的，所以设置所有的状态映射，并弄清楚如何连接到正确的实例是很棘手的。

该工具的文档很棒，但是本博客将给出如何进行迁移的分步方法。

## 获取工具

本文开头的 Github 链接是该工具所有文档的起点。要直接下载该版本，请将浏览器指向[solidify/jira-azuredevops-Migrator/releases](https://github.com/solidify/jira-azuredevops-migrator/releases)。

## 为吉拉出口做准备

Github repo 包含在此位置的[样本。值得注意的是，配置文件的设置是必不可少的。参见下面文件的前几行:](https://github.com/solidify/jira-azuredevops-migrator/blob/master/docs/Samples/config-scrum.json) 

```
"source-project": "MyJiraProject", "target-project": "DevOps Project Name", "query": "project = 'MyJiraProject'", "workspace": "C:\\Temp\\JiraExport\\", "epic-link-field": "Epic Link", "sprint-field": "Sprint", "download-options": 7, "batch-size": 20, "log-level": "Info", "attachment-folder": "Attachments", "user-mapping-file": "", "base-area-path": "", "base-iteration-path": "", "ignore-failed-links": true, "process-template": "Agile", 
```

如您所见，源和目标字段分别对应于您在吉拉和 DevOps 中的数据。流程模板字段对应于您在 DevOps 中设置的内容，如 CMMI、Scrum 或敏捷(或基础)。

设置旁边是映射。您可能知道，在吉拉和 DevOps 中，一些任务、状态或其他属性是不同的。假设您在吉拉有一个状态为 **Review** 的任务。您可能在 DevOps 中没有相同的状态，所以您希望**将它映射到另一个状态类型。这同样适用于“用户故事”或“史诗”** 

```
"type": [{ "source": "Feature", "target": "Feature" }, { "source": "Epic", "target": "Epic" }, { "source": "Story", "target": "User Story" }, { "source": "Bug", "target": "Bug" }, { "source": "Task", "target": "User Story" }, { "source": "Sub-task", "target": "Task" }] 
```

在配置文件中，您应该指定所有这些映射。如果添加失败，导出工具将生成错误，因此您可以添加缺少的映射并重新导出吉拉。

## 运行吉拉导出工具

如果设置了配置文件，您可以使用以下命令运行该工具:

```
jira-export.exe -u p.rombouts@yourdomain.com -p api-token-here --url https://yourinstance.atlassian.net --config config.json 
```

如您所见，我已经使用 API 令牌设置了该命令。请参考[本页](https://github.com/solidify/jira-azuredevops-migrator/blob/master/docs/overview.md)关于如何获得吉拉的 API 令牌。从理论上讲，密码应该可以用，但在我的案例中却不行。

## 检查导出结果

当工具运行时，您将直接在您的 CLI 界面中看到结果:

```
$ jira-export.exe -u username -p apitoken --url https://something.atlassian.net --config config.json Jira Exporter v2.2.25 Copyright (c) Solidify Wow, there's a newer release out (v2.2.31)! We recommend downloading it for latest features and fixes. [I][10:12:11] Connecting to Jira... [I][10:12:11] Retrieving Jira fields... [I][10:12:12] Retrieving Jira link types... [I][10:12:12] Export started. Exporting 705 items. [I][10:12:13] Initializing Jira field mapping... [I][10:12:14] Processing 1/705 - 'Item-790'. [W][10:12:14] Could not find user 'someuser1@yourdomain.com' identity in user map. Using original identity 'someuser1@yourdomain.com'. [W][10:12:15] Could not find user 'someuser2@yourdomain.com' identity in user map. Using original identity 'someuser2@yourdomain.com'. [I][10:12:15] Processing 2/705 - 'Item-789'. 
```

从上面的导出开始可以看出，在映射中找不到一些用户。这是我故意做的；我想从吉拉完全相同的用户在 Azure DevOps 中使用。或者，您可以创建一个 users.txt 文件，并将吉拉的用户映射到 Azure DevOps 中的不同用户。如果缺少任何字段或映射，您也会在控制台中看到。

**奖励**:在 config 工具指定的导出目录下，输出也保存到一个类似**jira-export-log-190809-101211 . txt**的文件中。所以你不需要自己保存输出，只是让它滚动！。导出可能需要相当长的时间，所以坐下来喝杯咖啡。

## 运行 Azure DevOps 导入工具

如果上一步顺利完成，并且日志文件中一切正常，您可以将吉拉导出的数据导入 Azure DevOps。

再次使用相同的配置文件，您可以运行以下命令:

```
wi-import --token personal-access-token-here --url https://yourorganisation.visualstudio.com --config config.json --force 
```

如您所见，我已经使用个人访问令牌设置了该命令。请参考[本页](https://github.com/solidify/jira-azuredevops-migrator/blob/master/docs/overview.md)了解如何为 Azure DevOps 获取一个。

同样，该工具将为您提供详细的信息，并且还会将一个日志文件转储到吉拉导出文件所在的文件夹中。

如果一切顺利；您现在可以使用 Azure DevOps 了！

## 定稿和清理

明智的做法是和你的团队一起提前做好计划。您应该有一个截止日期，并确保在您完成导出/导入后，对旧吉拉项目的访问是只读的或关闭的。你确实**不想**为一个团队维持两个董事会！

## 结论

希望这本手册对你有用，这个工具帮了我大忙。文档是优秀的，并且由优秀的 Solidify 团队积极维护。对你们真是感激不尽！