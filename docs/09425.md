# 带 SSL 终端的代理后面的 Openshift

> 原文：<https://dev.to/livioribeiro/openshift-behind-a-proxy-with-ssl-termination-4gd8>

Openshift 容器平台是在容器环境中部署应用程序和服务的绝佳工具:它隐藏了 Kubernetes 的复杂性，并提供了许多工具来管理构建、配置、卷和环境变量。

一切都很好，直到我遇到这个奇怪的问题，我不能让 Keycloak 正常工作。我无法认证，因为服务一直告诉我，它需要 HTTPS...我有 HTTPS！

为了追踪这个问题，我写了一个小服务(在 [Rust](https://www.rust-lang.org/) :)返回它收到的 HTTP 头，结果是这样的:

```
[other headers]
X-Forwarded-Port: 80
X-Forwarded-Proto: http
[some more headers] 
```

Enter fullscreen mode Exit fullscreen mode

太奇怪了！代理发送了正确的`X-Forwarded-*`报头，但是在某个地方它们被覆盖了！

我搜索了这一行为，发现[这是关于改变 openshift 路由器配置的](https://access.redhat.com/solutions/3986281)(需要一个 redhat 账号来可视化)。我的解决方案有点不同，但效果相同。

我们需要根据文档创建一个定制的路由器配置。首先让我们得到默认配置的副本:

```
oc -n default rsh dc/router cat haproxy-config.template > haproxy-config.template 
```

Enter fullscreen mode Exit fullscreen mode

现在打开`haproxy-config.template`文件，搜索以下文本:

```
http-request set-header X-Forwarded-Port %[dst_port]
http-request set-header X-Forwarded-Proto http if !{ ssl_fc }
http-request set-header X-Forwarded-Proto https if { ssl_fc }
http-request set-header X-Forwarded-Proto-Version h2 if { ssl_fc_alpn -i h2 } 
```

Enter fullscreen mode Exit fullscreen mode

上面这几行覆盖了代理发送的`X-Forwarded-*`头。我的解决方案是添加一个条件，只设置不存在的标题:

```
http-request set-header X-Forwarded-Port %[dst_port]  if { req.hdr_cnt(X-Forwarded-Port) eq 0 }
http-request set-header X-Forwarded-Proto http        if { req.hdr_cnt(X-Forwarded-Proto) eq 0 } !{ ssl_fc }
http-request set-header X-Forwarded-Proto https       if { req.hdr_cnt(X-Forwarded-Proto) eq 0 } { ssl_fc }
http-request set-header X-Forwarded-Proto-Version h2  if { req.hdr_cnt(X-Forwarded-Proto-Version) eq 0 } { ssl_fc_alpn -i h2 } 
```

Enter fullscreen mode Exit fullscreen mode

文件更改后，现在我们可以将它添加到路由器:

```
# create a configmap to hold the file
oc -n default create configmap customrouter --from-file=haproxy-config.template
# add the configmap as a volume
oc -n default set volume dc/router --add --overwrite \
    --name=config-volume \
    --mount-path=/var/lib/haproxy/conf/custom \
    --source='{"configMap": { "name": "customrouter"}}'
# set an environment variable to tell the router were to find the configuration
oc -n default set env dc/router \
    TEMPLATE_FILE=/var/lib/haproxy/conf/custom/haproxy-config.template 
```

Enter fullscreen mode Exit fullscreen mode

之后 Openshift 会重新部署路由器，配置生效。