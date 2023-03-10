# 🛠野花作为窗口服务

> 原文：<https://dev.to/orestispantazos/wildfly-as-windows-service-456c>

WildFly 应用服务器是通过 DevOps 配置的，有时这意味着他们需要配置为 windows 服务，这是另一种在后台运行服务器的技术。有如下多个步骤:

**第一步:**

*将文件复制到 WildFly 目录下*

1.  前往*wildly _ HOME _ DIR*/docs/contrib/scripts
2.  将**服务**目录复制到 C:\ Servers \ wild fly-11 . 0 . 0 . final \ bin

**第二步:**

*更新服务批处理文件*中的设置

复制到当前 WildFly 目录的文件是 **service.bat** 文件，准备修改。

1.  转到*wildly _ HOME _ DIR*\ bin
2.  打开 **service.bat** 文件，用 Notepad++在编辑模式下打开

**提示:****显示名称**将出现在 windows 服务列表中，因此，如果管理员准备在同一台 Windows 机器上安装多台服务器，然后希望使用更有意义的内容更新这些设置，如下所述:

`If developers team have DEV and PROD environment that may want to update the settings to the following:`

```
DISPLAYNAME=WildFly Application Server 11.0.0.Final Development
DISPLAYNAME=WildFly Application Server 11.0.0 Final Production 
```

Enter fullscreen mode Exit fullscreen mode

`Note: The SHORTNAME needs to be unique`

**可选:**为防止内存丢失问题，管理员需要修改`JAVA_OPTS`设置以增加内存。

```
Current Setting: JAVAOPTS=-Xrs
New Setting: JAVAOPTS=-Xmx1024M -Xms512M -XX:MaxPermSize=512M -Xrs 
```

Enter fullscreen mode Exit fullscreen mode

**第三步:**

*安装 Windows 服务*

1.  以管理员身份打开并运行 Windows CMD
2.  转到 WildFly bin 目录

`$ cd WILDFLY_HOME_DIR\bin`

**注意:**如果管理员设置了 **JBOSS_HOME** 系统变量，可以替换为带有 **%JBOSS_HOME%** 的野目录路径

输入以下命令安装服务:

`$ service install`

**第四步:**

*更新 Windows 服务属性*

1.  转到**开始**并选择**控制面板**
2.  点击控制面板中的**系统和安全**
3.  点击**管理工具**
4.  双击**服务**显示当前服务器上安装的本地服务列表
5.  滚动到名为 *WildFly 应用服务器*的服务
6.  右键单击服务名称以显示弹出菜单
7.  选择**属性**打开*属性窗口*
8.  在**启动类型**处，选择自动。
9.  点击**开始**开始 WildFly 服务
10.  对话窗口将打开，显示启动服务的进度，一旦启动过程完成，点击 **OK** 保存更改。

`Check and verify if Windows Service is started properly of WildFly Server.`

**重要提示:**出现在服务列表中的实际名称取决于您在 *service.bat* 文件中为**显示名称**设置输入的内容

**第五步:**

最后但同样重要的是，最后一个配置应该修改为所有 IP 地址都可以访问。

*   转到保存在`C:\Servers\wildfly-11.0.0.Final\standalone\configuration`路径中的`standalone.xml`配置文件，用 Notepad++或 Visual Studio 代码打开

``bash
<interface name="public">
<inet-address value="${jboss.bind.address:0.0.0.0}"/>
</interface>`` 

 ``来源:
[https://opendevops.dev/wildfly-as-windows-service/](https://opendevops.dev/wildfly-as-windows-service/)``