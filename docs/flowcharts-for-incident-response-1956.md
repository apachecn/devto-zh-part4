# 事故响应流程图

> 原文：<https://dev.to/lbonanomi/flowcharts-for-incident-response-1956>

这听起来熟悉吗？

> 警报在闪烁。客户在发电子邮件。
> 上层管理在召唤。
> 直线经理正在做决策。
> 
> ...

让我们从系统架构师那里借用 [Graphviz](https://www.graphviz.org) 工具来制定更容易遵循的事件响应计划。

```
digraph Jira_Outage 
{
  {
    first_notification [ 
      label="Notification of a Jira outage from any channel" 
      shape=rect
    ] 

    pc_browser [ 
      label="Does Jira load in a PC Browser?" 
      shape=rect 
    ]
    yes_pc_browser [ 
      label="Yes" 
      shape=rect 
    ]

    no_pc_browser [ 
      label="No" 
      shape=rect 
    ]

    linux_server_browser [ 
      label="Does Jira load in\nFirefox from a Linux server?" 
      shape=rect 
    ]

    user_ssh_connect [
      label="Does SSH-ing to jira.internal.hostname connect?" 
      shape=rect 
    ]

    yes_user_ssh_connect [ 
      label="Yes"
      shape=rect
    ]

    no_user_ssh_connect [
      label="No"
      shape=rect 
    ]

    debug_screen [
        label="Jira shows an unexpected screen."
        shape=rect
    ]

    pingable [
      label="Does jira.internal.hostname ping?"
      shape=rect
    ]

    host_down [
      label="Application host is down.\n\n\Attempt to restart application."
      shape=rect
    ]

    yes_ping [
      label="Yes"
      shape=rect
    ]

    no_ping [
      label="No"
      shape=rect
    ]

    site_down [
      label="Site is down.\n\nSwitch to backup site."
      shape=rect
    ]

    first_notification -> pc_browser

    pc_browser -> no_pc_browser
    pc_browser -> yes_pc_browser 
    no_pc_browser -> user_ssh_connect
    yes_pc_browser -> debug_screen

    user_ssh_connect -> no_user_ssh_connect
    user_ssh_connect -> yes_user_ssh_connect

    yes_user_ssh_connect -> linux_server_browser
    no_user_ssh_connect -> pingable

    pingable -> no_ping
    pingable -> yes_ping

    yes_ping -> host_down
    no_ping -> site_down

    linux_server_browser -> site_down
    debug_screen -> host_down

  }
} 
```

变成这样漂亮的教学流程图: