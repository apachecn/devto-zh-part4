# 我是现场詹金斯主持人吗？

> 原文：<https://dev.to/lbonanomi/am-i-the-live-jenkins-host-42nf>

我更喜欢使用集中的 Jenkins 来代替特定于主机的 crontabs 它更明显，而且你不能打败它的价格。为了增强可生存性，我喜欢让几个实例并行运行，并由 HAproxy 负责，但当我安排的作业只能安全运行一次时，这就出现了问题。

稍微用`curl -I`刺激一下，就会发现 Jenkins 实例在它们的响应头中返回一个散列的标识值，我们可以用它来标记特定的实例；如果脚本是从一个“热备份”Jenkins 实例中调用的，下面的代码块将自动中止它所添加到的任何 shell 脚本。

```
#!/bin/bash

LIVE=$(https_proxy="" curl -I -s -u $USERNAME:$PASSWORD https://$JENKINS_URL:8080 | awk '/X-Instance-Identity/ { print $2 }')
IAM=$(https_proxy="" curl -I -s -u $USERNAME:$PASSWORD http://127.0.0.1:8080 | awk '/X-Instance-Identity/ { print $2 }')

[[ "$LIVE" == "$IAM" ]] || exit 0 
```