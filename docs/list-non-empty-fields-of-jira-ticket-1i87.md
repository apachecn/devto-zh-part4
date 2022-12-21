# 列出吉拉票证的非空字段

> 原文：<https://dev.to/turbobasic/list-non-empty-fields-of-jira-ticket-1i87>

让我们假设您已经将使用吉拉 REST API 接收到的有效载荷保存到文件`jiraTask.json`中。

```
$ jiraGet issue/BOOM-666 > jiraIssue.json 
```

Enter fullscreen mode Exit fullscreen mode

> 你可以阅读更多关于超级简单的吉拉 API
> 
> [![turbobasic image](img/8ea4d06a4dbb5289964e23824a15e4ad.png)](/turbobasic) [## bash/zsh 的超级简单的吉拉 API 包装器
> 
> ### 安德烈·梅尔尼克·╔╬╚╗╠╠╣╔╦╣╚╗╔╬╗1919 年 8 月 2 日 2 分钟阅读
> 
> #jira #bash #zsh](/turbobasic/super-simple-jira-api-wrapper-for-bash-117h)

然后您可以使用下面的命令列出票据的非空字段(*确保您已经在*之前安装了**[【jq】](https://stedolan.github.io/jq/)**):

```
$ jq --raw-output '
    .fields | 
    . as $f |
    keys[] | 
    select($f[.] != null)
' jiraIssue.json

...<87 fields listed>... 
```

Enter fullscreen mode Exit fullscreen mode

在我的例子中，上面的命令列出了吉拉车票的 **87** 非空字段。将其与票据中的字段总数 **983** 进行比较😱

或者，如果您想查看字段及其值，请使用以下命令:

```
$ jq --raw-output '
    .fields | 
    . as $f | [ 
        keys[] | 
        select($f[.] != null) | 
        { (.): $f[.] } 
    ] | 
    add
' jiraIssue.json 
```

Enter fullscreen mode Exit fullscreen mode