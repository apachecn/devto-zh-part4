# Howto:下拉列表列出吉拉在 Jenkins 中的组件

> 原文：<https://dev.to/malexe/howto-drop-down-list-of-components-from-jira-2498>

在之前的关于[值的下拉列表](https://dev.to/malexe/howto-drop-down-list-for-values-2aop)中，我们学习了如何选择任何预定义的项目。
在这张收据上，我已经讲述了如何通过 API 从吉拉的正则表达式创建下拉列表来选择组件的名称。

先决条件:

*   [主动选择插件](https://wiki.jenkins.io/display/JENKINS/Active+Choices+Plugin)
*   吉拉证书

步骤 1:在作业配置中添加参数*主动选择被动参数*

步骤 2:为你的参数设置名称(例如*组件*

步骤 3:选择 groovy 脚本并添加下一个 Groovy 代码

```
import groovy.json.JsonSlurperClassic
import java.util.regex.*

def regexLine = "(((my|other)-(int|sys|bpl|pl|stp|tst)-([a-z,0-9,-]*)))"
def jiraUrl = "http://jira.domain.org"
def jiraProjectKey = JIRA_PROJECT

def juser = "JenkinsJiraUser"
def jpass = "PasswordOfJenkinsJiraUser"
def url = "curl -u ${juser}:${jpass} ${jiraUrl}/rest/api/2/project/${jiraProjectKey}/components"
def json = url.execute().text.replaceAll("\r\n", "")
def data = new JsonSlurperClassic().parseText(json)
def names = []
data.each{ component->
    if (component) {
        Matcher componentsMatcher = Pattern.compile(regexLine).matcher(component.name)
        if( componentsMatcher ) {
            names += component.name
        }
    }
}
return names 
```

第四步:设置“参考参数”*JIRA _ 项目*

第五步:享受！