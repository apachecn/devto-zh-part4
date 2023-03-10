# 我如何在 LTS 的 Ubuntu 18.04.1 中安装甲骨文 JDK 8

> 原文：<https://dev.to/bhuvanaguna/how-i-installed-oracle-jdk-8-in-ubuntu-18-04-1-lts-53jh>

今天我试着在我的新 Ubuntu 系统中安装 JAVA，并按照常规方法通过 ppa 下载:webupd 8 team/JAVA Oracle-JAVA 8-installer 点击[此链接](https://www.digitalocean.com/community/tutorials/how-to-install-java-with-apt-get-on-ubuntu-16-04)。

即使我成功安装了默认的 JDK 和 JRE，我也无法安装 Android Studio 所需的 Oracle JDK。

经过一番调查，我通过[这个链接](https://launchpad.net/~webupd8team/+archive/ubuntu/java)发现，从 2019 年 4 月 16 日起，如果不登录甲骨文账户，您将无法下载甲骨文 JDK。[这个](https://stackoverflow.com/questions/55920389/e-package-oracle-java8-installer-has-no-installation-candidate) stackoverflow 的回答也帮了我。

在所有这些帮助下，这些是我安装 Oracle JDK 的步骤。

*   创建或登录 Oracle 帐户后，从[此处](https://www.oracle.com/technetwork/java/javase/downloads/java-archive-javase8-2177648.html)下载最新的 JAVA 8 SE 开发套件。

*   打开你的终端，输入

    ```
    $mkdir /opt/jdk 
    ```

*   在新文件夹中解压 Java 文件

    ```
    $tar -zxf jdk-8u5-linux-x64.tar.gz -C /opt/jdk 
    ```

*   我使用这些命令安装(不要忘记用您安装的命令替换。)

    ```
    $update-alternatives --install /usr/bin/java java /opt/jdk/jdk1.8.0_<YourVersion>/bin/java 100
    $update-alternatives --install /usr/bin/javac javac /opt/jdk/jdk1.8.0_<YourVersion>/bin/javac 100 
    ```

*   要将 oracle JDK 设置为默认 JVM，运行

    ```
    $sudo update-alternatives --config java 
    ```

    并选择您安装的 Oracle JDK。对于 java 编译器也是如此，

    ```
     $sudo update-alternatives --config javac 
    ```

*   使用以下命令设置 JAVA_HOME 环境变量。

    ```
     $sudo nano /etc/environment 
    ```

    添加这一行

    ```
    JAVA_HOME="/opt/jdk/jdk1.8.0_<YourVersion>/bin/java" 
    ```

    并保存文件，退出。现在使用
    重新加载并检查

    ```
    $source /etc/environment

    $echo $JAVA_HOME 
    ```

*   检查 java 版本是否正确

    ```
    $java -version 
    ```

希望这对你有帮助。