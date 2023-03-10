# 🛠设置 WildFly SSL

> 原文：<https://dev.to/orestispantazos/setup-wildfly-ssl-45om>

出于安全原因，DevOps 应该在 WildFly 应用服务器上配置 SSL 支持。以下步骤描述了如何在本地服务器上为 web 应用程序配置 HTTPS:

**第一步:**

*生成密钥库和自签名证书*

确保在`JAVA_HOME`上正确安装和设置 Java，因为 JRE keytool 将用于此目的。

切换到命令行并执行如下所示的命令:

`$ keytool -genkey -alias mycert -keyalg RSA -keystore mycert.keystore -validity 365`

前面提到的命令有一些默认设置，也提示开发人员输入如下所示的附加信息:

```
What is your first and last name?
  [Unknown]:  Orestis Pantazos
What is the name of your organizational unit?
  [Unknown]:  Open DevOps
What is the name of your organization?
  [Unknown]:  opendevops.dev
What is the name of your City or Locality?
  [Unknown]:  Athens
What is the name of your State or Province?
  [Unknown]:  Greece
What is the two-letter country code for this unit?
  [Unknown]:  GR
Is CN=Orestis Pantazos, OU=Open DevOps, O=opendevops.dev, L=Athens, ST=Greece, C=GR correct?
  [no]:  yes 
```

Enter fullscreen mode Exit fullscreen mode

**第二步:**

该命令在您当前工作的文件夹中生成 mycert.keystore 文件。将它复制到你的 WildFly 配置目录(`%JBOSS_HOME%/standalone/config`)

**第三步:**

*配置额外的 WildFly 安全领域*

下一步是在`standalone.xml`的 wildly security-realm 部分将新的 keystore 配置为 SSL 的服务器标识。您可以在 XML 文件中的`<management>`标签之后以及`<security-realms>`标签内部插入源代码。

```
<management>
    <security-realms>
        <security-realm name="UndertowRealm">
            <server-identities>
                <ssl>
                    <keystore path="mycert.keystore" relative-to="jboss.server.config.dir" keystore-password="secret" alias="mycert" key-password="secret"/>
                </ssl>
            </server-identities>
        </security-realm> 
```

Enter fullscreen mode Exit fullscreen mode

**第四步:**

*为 SSL 配置回流子系统*

如果默认服务器正在运行，将 https 监听器添加到回流子系统:

```
<subsystem xmlns="urn:jboss:domain:undertow:1.2">
    <server name="default-server">
        <https-listener name="https" socket-binding="https" security-realm="UndertowRealm"/> 
```

Enter fullscreen mode Exit fullscreen mode

对于给定名称空间中的 https 侦听器，仅将单词`UndertowRealm`替换为前面的单词`security-realm="..."`。

**第五步:**

当前实例的 SSL 端口已经用于`https://localhost:8443/`中的连接。否则，可以将 SSL 端口更改为 443，作为文件结尾/底部的默认端口号。

来源:
[https://opendevops.dev/setup-wildfly-ssl/](https://opendevops.dev/setup-wildfly-ssl/)