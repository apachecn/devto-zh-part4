# how to:Jenkins 中 Gitlab 项目的下拉列表

> 原文：<https://dev.to/malexe/howto-drop-down-list-with-gitlab-projects-in-jenkins-27k3>

在以前的收据关于下拉列表的价值，我们学习了如何选择任何组件从吉拉。但在吉拉，т在现实生活中往往并不需要发展过程。在这张收据上，我已经讲述了如何通过 API 从 Gitlab 创建下拉列表，通过正则表达式选择项目名称。

先决条件:

*   [主动选择插件](https://wiki.jenkins.io/display/JENKINS/Active+Choices+Plugin)
*   API 的 Gitlab 令牌

步骤 1:在作业配置中添加参数主动选择反应参数

步骤 2:为参数设置名称(例如组件)

步骤 3:选择 groovy 脚本并添加下一个 Groovy 代码

```
import groovy.json.JsonSlurperClassic
import java.util.regex.*

def projectNamespace = JIRA_PROJECT.toLowerCase()
def regexLine = "^((${projectNamespace}-(int|sys|service|bpl|pl|stp|tst)-([a-z,0-9,-]*)))"
def gitlabUrl = "https://gitlab.domain.org"
def gitlabApiToken = "secretToken"
def per_page = 128

def data = []
def url = "curl -k -H PRIVATE-TOKEN:${gitlabApiToken} ${gitlabUrl}/api/v4/projects?per_page=${per_page}"
def json = url.execute().text.replaceAll("\r\n", "")
data += new JsonSlurperClassic().parseText(json)

def list = []
data.each { project ->
    Matcher projectMatcher = Pattern.compile(regexLine).matcher(project.path)
    if(projectMatcher && project.namespace.path == projectNamespace) {
        list += project.path
    }
}
return list.sort() 
```