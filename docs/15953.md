# 使用 plink 设置 SSH 隧道

> 原文：<https://dev.to/cambiph/setting-up-an-ssh-tunnel-using-plink-4om7>

在我们公司，我们只能使用标准端口连接到外部世界。防火墙阻止了端口 8081 的传出流量。这真是让人头疼。我们需要将 Javascript 库发布到一个 Nexus 实例。这个实例运行在云中，您猜对了，端口 8081。

我们已经在使用 Jenkins 管道来自动化我们的工作流程。决定在管道中增加一个新的步骤:ssh 隧道。我们的 Jenkins 实例运行在 Windows Server 2012 R2 中。使用 bash 创建隧道是无法实现的。我们看了一下 Powershell 来建立 SSH 隧道，但是似乎 SSH 在 Powershell 中的实现还不够成熟。批量脚本来拯救！

我们大多数人都使用 Putty 来处理 SSH 会话。制作 Putty 的人也制作 plink，这是 Putty 后端的命令行界面。结合批处理文件，我们可以创建一个 SSH 隧道，如下所示:

```
plink -v -x -a -T -C -noagent -ssh -pw “password” -L 8081:our-nexus-instance.com:8081 username@our-nexus-instance.com 
```

让我解释一下上面命令中使用的不同标志:

```
-v : show verbose messages
-x: enable X11 forwarding
-a: enable agent forwarding
-T: disable pty allocation
-C: enable compression
-noagent: disable use of Pageant (only needed when using SSH-keys)
-ssh: use the SSH-protocol
-pw: provide the password
-L: forward local port to remote address 
```

使用-L 标志，我们可以将一个本地端口映射到一个远程端口。这是使用以下语法完成的:::。接下来是我们想要连接的主机名，前缀是用户名，用于连接:username@remoteHost。

通过将这个命令插入到我们的 Jenkinsfile 中，我们可以打开到我们的远程 Nexus 实例的隧道:

```
stage(‘Publish’) {
 steps {
  bat “plink -v -x -a -T -C -noagent -ssh -pw ‘password -L 8081:our-  
       nexus-instance:8081 username@our-nexus-instance.com”
   }
 } 
```