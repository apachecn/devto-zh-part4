# 在基于 linux 的操作系统中配置 IPFS

> 原文：<https://dev.to/azwyane/configuring-ipfs-in-linux-based-os-72g>

星际文件系统(IPFS)是用于在分布式文件系统中存储和共享数据的协议和对等网络，它使用内容寻址来唯一地标识连接所有计算设备的全局命名空间中的每个文件。IPFS 不仅允许用户接收内容，还允许用户托管内容，就像 BitTorrent 一样。与位于中央的服务器不同，IPFS 是围绕一个分散的用户-操作员系统构建的，这些用户-操作员持有全部数据的一部分，创建了一个有弹性的文件存储和共享系统。网络中的任何用户都可以通过文件的内容地址提供文件，网络中的其他对等点可以使用分布式哈希表(DHT)从任何拥有该内容的节点找到并请求该内容。来源:[维基百科](https://en.wikipedia.org/wiki/InterPlanetary_File_System)

> 我想你已经清楚 IFPS 是什么了。对于那些寻求分布式网络系统，用于共享和接收文件，在本地发展组织，构建 dApps，并使互联网成为一个更好的地方，就像过去一样，在技术巨头掌权和集中管理之前。互联网及其资源对这个世界上的每个人都是平等的。

在这里，我将教你如何配置，你必须考虑到我正在使用:

*安装 Golang

*拥有一个基于 debian 的 linux 发行版

## 安装 GOLANG:

首先，你必须在你的工作设备中安装 go。访问网站[转到](https://golang.org/)下载档案并

将其提取到`/usr/local`，在`/usr/local/go`中创建一个 Go 树。

例如:

```
tar -C /usr/local -xzf go1.13.1.linux-amd64.tar.gz 
```

Enter fullscreen mode Exit fullscreen mode

注意:需要运行 sudo。

现在，我们需要将`/usr/local/go/bin`添加到`PATH`环境变量中。您可以将这一行添加到您的`/etc/profile`(对于系统范围的安装)或`$HOME/.profile` :
中

```
export PATH=$PATH:/usr/local/go/bin 
```

Enter fullscreen mode Exit fullscreen mode

立即应用更改，只需直接运行 shell 命令，或者使用诸如`source $HOME/.profile`之类的命令从概要文件中执行它们。

接下来，在您的工作区中创建目录`src/hello`，并在该目录中创建一个名为`hello.go`的文件，看起来像:

```
package main

import "fmt"

func main() {
 fmt.Printf("hello, world\n")
} 
```

Enter fullscreen mode Exit fullscreen mode

然后用`go`工具构建它:

```
$ cd $HOME/go/src/hello
$ go build 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令将在源代码旁边的目录中构建一个名为`hello`的可执行文件。执行它以查看问候语:

```
$ ./hello
hello, world 
```

Enter fullscreen mode Exit fullscreen mode

如果您看到“hello，world”消息，则您的 Go 安装正在运行。

来源: [Golang](https://golang.org/)

下一个过程是关于安装 IPFS，到我的下一个博客[这里](https://dev.to/azwyane/configuring-ipfs-part-2-227c)。
T3![](img/46e1f960b14737e1f4d7a4869225af79.png)T5】