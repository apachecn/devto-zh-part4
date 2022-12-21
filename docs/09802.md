# 解决 minishift 新鲜实例问题

> 原文：<https://dev.to/livioribeiro/solving-minishift-fresh-instance-problems-5a5j>

当我开始使用 minishift 时，我对 Openshift 没有多少经验。我在一些问题中挣扎，我不知道发生了什么。这些是问题:

*   生成未能推送到注册表
*   提取映像时部署受阻
*   无法通过路由访问应用程序
*   无法以管理员身份登录 web 控制台

前三个问题是由“默认”名称空间上的“docker-registry”和“router”部署无法启动引起的。

这些问题的解决方案非常简单，只需要在终端上输入几个命令:

```
# login first as admin user
oc login -u system:admin
# restart "docker-registry"
oc -n default rollout latest dc/docker-registry
# restart "router"
oc -n default rollout latest dc/router
# allow "admin" user to log in to the web console
oc adm policy add-cluster-role-to-user cluster-admin admin 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以使用 minishift 工作，不再有任何问题！