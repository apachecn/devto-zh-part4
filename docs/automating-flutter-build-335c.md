# 自动化颤振构建

> 原文：<https://dev.to/tomavelev/automating-flutter-build-335c>

最近，我已经自动构建了我的一个 Flutter 项目，并且我计划继续其他的 Flutter 项目。我使用 Git 作为源代码管理系统。为了让自己跟上当前的潮流，我使用了 Docker。

首先我从这里抓起容器:[https://github.com/jenkinsci/docker/blob/master/README.md](https://github.com/jenkinsci/docker/blob/master/README.md)

我使用脚本将主机上的目录映射到容器中的目录:
`docker run -p 8080:8080 -p 50000:50000 -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts`

这是让 Jenkins 开始做你想做的任何事情的起点——在我的例子中是 Java & Flutter 构建、部署、监控和修复东西、日常备份。你可以做任何你想做的事。

构建过程的一部分是在“原始”git 存储库中放置一个钩子。我已经更新了“post-update”文件——从远程脚本触发一个 Jenkins 构建，这样每次我推送东西时，如果有东西坏了，我都会收到警告。如果 Jenkins 作业有一个部署子例程，那么最好部署到一个开发环境中，尤其是当团队很大的时候。Jenkins 提供了将工作链接成链的可能性，因此如果一切正常，就可以继续进行。

接下来，我抓取了一个由这个聪明的家伙[https://medium.com/@kyorohiro](https://medium.com/@kyorohiro)共享的 docker 文件，这个文件中有 Flutter & Dart，我只是将“FROM”docker 容器源放在 Jenkins 中。以我个人的经验来看——需要一点点调整才能把东西粘在一起。有时会有缺失的依赖项、环境变量、错误输入的路径等，但是，除了少数最初失败的 Jenkins 构建，一切都正常。

我设置的流程是:

*   Git 将最新的源代码从挂钩的“源”拉到本地目录。
*   在上面的目录中执行 Flutter 构建
*   SFTP/SCP/上传到我的服务器

在 Flutter Web 的情况下，输出是一个充满文件的目录，所以我创建了一个简单的 Java 程序(Jar)——将来可用于生成多个文件的任何其他编译/构建任务——它遍历构建目录，并且仅当文件比最后一次修改/构建/更新时才上传文件。

作为一个写代码和探索“创新”东西的技术人员，我在自己和他人身上看到了——忘记任何技术的哲学任务——“自动化事物和最小化人类劳动”。自动化是聪明的做法。你不需要特殊的人工智能来将一遍又一遍重复的工作委派给计算机，这些工作每天都是一样的。