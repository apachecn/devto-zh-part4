# 使用替代方法在 Linux 上安装和管理多个 Java 版本

> 原文：<https://dev.to/thegroo/install-and-manage-multiple-java-versions-on-linux-using-alternatives-5e93>

在这篇文章中，我将指导您在开发 Linux 的机器上安装 Java。我决定写这篇文章，因为我得到了一些问题，如果我使用像 [Sdkman](https://sdkman.io/) 这样的东西来管理它，我该如何在我的开发环境中管理多个 java 版本，在这篇文章中，我将解释为什么。

在这个行业工作了 20 多年，我开发了许多不同语言的软件和脚本，如 JavaScript、Pascal、Go、Python 等，但我主要是一个热情的 Java 开发人员，我致力于帮助其他同事开始用 Java 编程，并帮助消除 Java 复杂或难以开始的谬论。Java 是用于开发复杂的企业软件的最常用的编程语言，目前为止，它拥有更好的生态系统，包括可用的库、ide 和工具。

我的首选开发环境是 Linux，所以我更愿意使用 Linux 替代产品[来管理 java SDK 安装，因为它内置在 Linux 中，不仅允许您管理 java，还允许您管理任何其他二进制文件，当使用 Linux 时，您可以在命令行中管理和访问这些文件。我将指导您安装 Java 11 并使用它运行您的第一个 Hello World 应用程序。](https://linux.die.net/man/8/alternatives)

完整的安装过程将使用命令行。所以让我们开始，打开一个终端控制台并`cd`到你的首选工作目录。

*   确保安装了 [wget](https://www.gnu.org/software/wget/wget.html) 。

```
sudo apt update && sudo apt install wget 
```

Enter fullscreen mode Exit fullscreen mode

*   下载 openjdk:

```
wget https://download.java.net/openjdk/jdk11/ri/openjdk-11+28_linux-x64_bin.tar.gz 
```

Enter fullscreen mode Exit fullscreen mode

> 可以在这里查看更新的 java 11 版本:[https://jdk.java.net/java-se-ri/11](https://jdk.java.net/java-se-ri/11)

*   下载完成后，添加权限:

```
chmod +x openjdk-11+28_linux-x64_bin.tar.gz 
```

Enter fullscreen mode Exit fullscreen mode

*   创建将安装 jdk 的文件夹

```
mkdir /usr/lib/jvm/open-jdk-11 
```

Enter fullscreen mode Exit fullscreen mode

*   将其解压缩到您刚刚创建的/usr/lib/jvm/open-jdk-11 文件夹中。

```
tar -xzf ./openjdk-11+28_linux-x64_bin.tar.gz -C /usr/lib/jvm/open-jdk-11 --strip-components=1 
```

Enter fullscreen mode Exit fullscreen mode

*   更新备选方案以添加 java、javac、jshell 和 jar

    *   列出已安装的版本

```
update-alternatives --list java 
```

Enter fullscreen mode Exit fullscreen mode

*   配置 java 安装

```
sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/open-jdk-11/bin/java 1 
```

Enter fullscreen mode Exit fullscreen mode

*   配置 javac 安装

```
sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/open-jdk-11/bin/javac 1 
```

Enter fullscreen mode Exit fullscreen mode

*   配置 jar 安装

```
sudo update-alternatives --install /usr/bin/jar jar /usr/lib/jvm/open-jdk-11/bin/jar 1 
```

Enter fullscreen mode Exit fullscreen mode

*   配置 jshell 安装

```
sudo update-alternatives --install /usr/bin/jshell jshell /usr/lib/jvm/open-jdk-11/bin/jshell 1 
```

Enter fullscreen mode Exit fullscreen mode

*   测试安装，获取 java 版本:

```
java -version 
```

Enter fullscreen mode Exit fullscreen mode

*   使用 jshell 测试安装

```
jshell 
```

Enter fullscreen mode Exit fullscreen mode

*   输入

```
System.out.println("Hello World"); 
```

Enter fullscreen mode Exit fullscreen mode

*   点击`Ctrl + D`退出。

如果您使用上述相同的过程安装了多个版本的 java，您可以使用替代方法在它们之间切换，

*   显示已安装的 java 版本

```
sudo update-alternatives --display java 
```

Enter fullscreen mode Exit fullscreen mode

*   配置您想要使用的版本:

```
sudo update-alternatives --config java 
```

Enter fullscreen mode Exit fullscreen mode

## 添加新版本并在它们之间切换

基于一些评论建议，我决定扩展帖子，安装第二个版本，并明确显示如何在它们之间切换。

最近我一直在使用 Azul JVMs，所以这次我将从 Azul 下载最新的 LTS Java 17 开放 JDK

*   用 curl 下载它，我把它放在我的$HOME/tmp 文件中:

```
curl -L https://cdn.azul.com/zulu/bin/zulu17.32.13-ca-jdk17.0.2-linux_x64.tar.gz > ~/tmp/zulu17.32.13-ca-jdk17.0.2-linux_x64.tar.gz 
```

Enter fullscreen mode Exit fullscreen mode

下载的文件夹中的 Cd。

*   创建将安装 jdk 的文件夹

```
sudo mkdir /usr/lib/jvm/azul-open-jdk-17 
```

Enter fullscreen mode Exit fullscreen mode

*   将其解压缩到您刚刚创建的/usr/lib/jvm/azul-open-jdk-17 文件夹中。

```
sudo tar -xzf ./zulu17.32.13-ca-jdk17.0.2-linux_x64.tar.gz -C /usr/lib/jvm/azul-open-jdk-17 --strip-components=1 
```

Enter fullscreen mode Exit fullscreen mode

*   检查 java 的配置选项

```
sudo update-alternatives --config java 
```

Enter fullscreen mode Exit fullscreen mode

您将在备选方案中看到一个已配置的 java 列表，新版本不在那里，因此我们需要像在本文开始时那样注册它，让我们为 java 和 javac 这样做，确保检查选择号并将新版本添加到下一个可用版本中，在继续之前按 enter 键继续当前版本，让我们现在注册我们下载的新版本。

[![Java versions installed alternatives](img/b76a0342826de0bc28e34efa60ce3ab5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vzK31RMh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/3e1efref4wyxx6kaeazk.png)

*   配置 java 安装

```
sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/azul-open-jdk-17/bin/java 2 
```

Enter fullscreen mode Exit fullscreen mode

> 最后的数字是优先级，您可以选择哪个更符合您的目标，默认情况下，最高优先级将被选中(较低的数字)。

*   对 javac 重复上述步骤，用`sudo update-alternatives --config javac`检查已安装的 javac，然后用下一个可用的参考对其进行配置。

```
sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/azul-open-jdk-17/bin/javac 2 
```

Enter fullscreen mode Exit fullscreen mode

*   配置 jar 安装

```
sudo update-alternatives --install /usr/bin/jar jar /usr/lib/jvm/azul-open-jdk-17/bin/jar 2 
```

Enter fullscreen mode Exit fullscreen mode

*   配置 jshell 安装

```
sudo update-alternatives --install /usr/bin/jshell jshell /usr/lib/jvm/azul-open-jdk-17/bin/jshell 2 
```

Enter fullscreen mode Exit fullscreen mode

现在您已经安装并配置了新的 JDK，您可以使用
在不同版本之间轻松切换

```
sudo update-alternatives --config java 
```

Enter fullscreen mode Exit fullscreen mode

从选择列表中选择您想要的版本号。

从显示的列表中键入您想要使用的版本的参考号，并使用`java -version`进行检查。对于 javac、jshell、jar 或任何其他您想要使用 linux 上的替代版本来管理多个版本的工具，也可以执行相同的操作。

现在，您可以从可用列表中选择您想要的选项。就这样，您完成了，并且有了一个可以工作的本地开发 java 环境。

如果你想快速开始使用 Spring Boot 在 Java 中创建一个 API，确保你已经安装了 git 和 maven】)并查看这个 [Spring Boot 速成班](https://dev.to/thegroo/spring-boot-crash-course-21nm)，它非常容易和快速跟随。