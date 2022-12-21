# Howto:包含 Gitlab 项目分支的下拉列表

> 原文：<https://dev.to/malexe/howto-drop-down-list-with-branches-from-gitlab-project-31cc>

在之前收到的关于值的下拉列表中，我们学习了如何选择吉拉的任何组件或 Gitlab 的项目名称。下一步，我们需要在分支选择器中选择项目。

先决条件:

*   [主动选择插件](https://wiki.jenkins.io/display/JENKINS/Active+Choices+Plugin)
*   API 的 Gitlab 令牌

步骤 1:在作业配置中添加参数主动选择反应参数

步骤 2:为参数设置名称(例如 GIT_BRANCH)

步骤 3:将引用参数的值设置为组件

步骤 4:选择 groovy 脚本并添加下一个 Groovy 代码

```
import groovy.json.JsonSlurperClassic

def projectName = COMPONENT
def gitlabUrl = "https://gitlab.domain.org"
def gitlabApiToken = "SecretToken"
def per_page = 128

def url = "curl -k -H PRIVATE-TOKEN:${gitlabApiToken} ${gitlabUrl}/api/v4/projects?search=$projectName"
def json = url.execute().text.replaceAll("\r\n", "")
def data = new JsonSlurperClassic().parseText(json)
def project = data.find { it.name == projectName }
def projectId = project.id

url = "curl -k -H PRIVATE-TOKEN:${gitlabApiToken} ${gitlabUrl}/api/v4/projects/${projectId}/repository/branches?per_page=${per_page}"
json = url.execute().text.replaceAll("\r\n", "")
data = new JsonSlurperClassic().parseText(json)
def list = []
data.each { branch ->
    list += branch.name
}
return list 
```