# Howto:如果您在名称空间中有 99 个以上的项目，那么可以使用 Jenkins 中的 Gitlab 项目下拉列表

> 原文：<https://dev.to/malexe/howto-drop-down-list-with-gitlab-projects-in-jenkins-if-you-have-more-them-99-projects-in-namespace-50hg>

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

def page = 1
def perPage = 64

def data = []
def list = []

while (true) {  
  paging = "&per_page=${perPage}&page=${page}"
  url = "curl -k -H PRIVATE-TOKEN:${gitlabApiToken} ${gitlabUrl}/api/v4/projects?search=${projectNamespace}-${paging}"
  json = url.execute().text.replaceAll("\r\n", "")
  data = new JsonSlurperClassic().parseText(json)
  if( data == [] ) { break }  
  data.each { project ->
    def projectMatcher = (project.name =~ regexLine)    
    if(projectMatcher.count && project.namespace.path == projectNamespace) {
      list += project.name
    }
  }
  page++
}

return list.sort() 
```