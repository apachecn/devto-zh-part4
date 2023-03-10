# bash/zsh 的超级简单的吉拉 API 包装器

> 原文：<https://dev.to/turbobasic/super-simple-jira-api-wrapper-for-bash-117h>

我经常需要一些非常简单的吉拉相关任务的自动化，而不需要大量的外部依赖，比如 Python 版本&包、Groovy 类或者甚至更奇特的东西。下面提供的脚本只依赖于 Bash v4+或 Zsh v5+(或者更早的版本，我没有测试过)。

*(Ups...在**上收拢**也是一样。 **[jq](https://stedolan.github.io/jq/)** 仅用于花式输出)*

## 例子

```
$ echo '{
    "fields": {
      "project": { "key": "BOOM" }, 
      "summary": "Foo", 
      "description": "Bar", 
      "labels": ["test"], 
      "issuetype": { "name": "Issue" } 
    }
  }' | jiraPost issue
{"id":"3162226", "key":"BOOM-666", "self":"https://example.com/jira/rest/api/2/issue/3162226"}

$ jiraGet issue/BOOM-666 | jq
{
  "id": "3162226",
  "self": "https://example.com/jira/rest/api/2/issue/3162226",
  "key": "BOOM-666",
  "fields": {
    "summary": "Foo",
    ...
}

$ jiraPut issue/BOOM-666 '{"fields": {"summary": "Bar"}}' 
```

Enter fullscreen mode Exit fullscreen mode

## 剧本`jira.sh`

这应该来源于您的 shell 配置文件

```
function jiraApi() {
    DATA_PARAM='-'

    if [[ PUT == ${1} || POST == ${1} ]]
    then DATA_PARAM='@-'
    fi

    if [[ -z ${3} ]]
    then curl  --user "${JIRA}" \
              --cert "${JIRA_CERT}" \
              --key "${JIRA_KEY}" \
              --cacert "${JIRA_CA}" \
              --header 'Content-Type: application/json' \
              --request "${1}" \
              --data "${DATA_PARAM}" \
              --silent \
              "${JIRA_API}/${2}"
    else echo "${3}" |
        curl  --user "${JIRA}" \
              --cert "${JIRA_CERT}" \
              --key "${JIRA_KEY}" \
              --cacert "${JIRA_CA}" \
              --header 'Content-Type: application/json' \
              --request "${1}" \
              --data "${DATA_PARAM}" \
              --silent \
              "${JIRA_API}/${2}"
    fi
}

function jiraGet() {
    jiraApi GET "${@}"
}

function jiraPost() {
    jiraApi POST "${@}"
}

function jiraPut() {
    jiraApi PUT "${@}"
} 
```

Enter fullscreen mode Exit fullscreen mode

## 环境变量

如果你问*‘那些 JIRA 变量是什么？’* —你明白了:这是保存所有敏感访问信息的地方。将它们也放入您的 shell 配置文件:

```
export JIRA='login:password'
export JIRA_API=https://example.com/jira/rest/api/2
export JIRA_CERT=/full/path/to/certificate/file
export JIRA_KEY=/full/path/to/private/key/file
export JIRA_CA=/full/path/to/certificate/authority/file 
```

Enter fullscreen mode Exit fullscreen mode