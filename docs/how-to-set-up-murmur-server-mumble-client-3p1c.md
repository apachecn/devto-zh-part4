# 杂音服务器如何设置杂音服务器/杂音客户端

> 原文：<https://dev.to/djemos/how-to-set-up-murmur-server-mumble-client-3p1c>

这份“Mumble &杂音”语音聊天应用指南展示了如何在 Slackel，Salix 上设置杂音服务器，这样一群用户就可以使用
mumble 客户端相互语音聊天。本指南中使用的软件包括:

*   杂音(服务器应用程序)
*   mumble(群组语音聊天应用程序)

==什么是 Mumble？==
Mumble 是一款针对群组的语音聊天应用。虽然它可以用于任何类型的活动，但它主要用于游戏。
可以比作 Ventrilo 或者 TeamSpeak 之类的程序。人们倾向于简化事情，所以当他们谈论 Mumble 时，他们要么谈论“Mumble”
客户端应用程序，要么谈论“Mumble &杂音”整个语音聊天应用程序套件。

==什么是杂音？==
“杂音”是服务器应用的名称。在任何情况下，如果有人谈论杂音，它肯定是服务器部分。

==获取一个 dyndns 域==

*   获取一个 dns 域，并通过动态 DNS 提供商(如 noip.com)创建一个
*   使用路由器接口的端口转发 tcp、udp 端口 64738

之后，我们准备继续。

==获取和安装杂音和咕哝==

安装杂音和咕哝与通常的命令:

```
 slapt-get -u
   slapt-get -i murmur
   slapt-get -i mumble 
```

Enter fullscreen mode Exit fullscreen mode

==设置杂音服务器==

为了方便起见，您只需运行/usr/doc/brunh-1 . 2 . 19/config-helper . sh 上的脚本来创建 mumble-server 组和 mumble-server 用户:

```
 sudo sh /usr/doc/murmur-1.2.19/config-helper.sh 
```

Enter fullscreen mode Exit fullscreen mode

上述脚本还将创建:

*   /var/log/mumble-server
*   /var/lib/mumble-server
*   /var/run/mumble-server 并由 mumble-server 组、mumble-server 用户对其进行修改

接下来打开为 root/etc/brunh . ini

更改以下变量
database = to database =/var/lib/mumble-server/mumble-server . SQLite
# log file = brunh . log to log file =/var/log/mumble-server/mumble-server . log
# PID file = to PID file =/var/run/mumble-server/mumble-server . PID
以上变量是在安装时设置的，我们在这里只是提一下让用户了解一下。欢迎来到这个运行杂音的服务器。祝您住得愉快！to welcometext= "随你喜欢！"
设置这一点很重要，因为这是您的服务器将出现在您所在国家国旗下的公共列表中的名称
# register password = secret to register password = secret
# register URL =[http://mumble.sourceforge.net/](http://mumble.sourceforge.net/)to register URL = " your _ DNS _ domain "(例如，register URL =[http://my server . noip . me](http://myserver.noip.me))

*   设置这个很重要，否则没人能找到你。
*   这是你的 ip。也把它放在你的路由器中，以便在每次你的 ip 改变时更新。

#uname= to uname=mumble-server

该变量也在安装过程中设置
保存文件/etc/brunh . ini
并以 root 身份启动 brunh 服务器

```
 sudo chmod 755 /etc/rc.d/rc.murmur 
      sudo /etc/rc.d/rc.murmur  start 
```

Enter fullscreen mode Exit fullscreen mode

即使你以根用户身份启动杂音服务器，它也会切换到杂音服务器用户

几分钟后，您的杂音服务器将出现在“公共互联网”中，在您的国旗下，您的名称在 registerName= variable 中

===设置超级用户密码= = =

现在您需要设置超级用户密码。此时服务器应该正在运行。打开终端，以 root 用户身份写入

```
 sudo murmurd -ini /etc/murmur.ini -supw  your-password 
```

Enter fullscreen mode Exit fullscreen mode

我们将需要这个来建立房间，注册用户，踢用户等

==设置 mumble 客户端==

当用户运行 mumble 客户端。它在菜单的互联网程序组中。

当 mumble 出现时，点击服务器->连接

出现一个窗口。您将在 LAN 下看到您的服务器名称。

*   右键单击您的服务器名称，然后单击“添加到收藏夹”。
*   在“收藏夹”下，右键单击您的服务器名称，然后单击“编辑”
*   删除“地址”旁边的行，并在那里写入 registerUrl 中的内容“不带 http://”(例如 myserver.noip.me)
*   在“端口”旁边写下 64738
*   在“用户名”旁边写下你喜欢的昵称
*   点击“确定”
*   点击“连接”
*   在下一条警告消息中，接受证书。点击“是”

恭喜
您已经连接到您的杂音服务器。

===以超级用户身份连接===

*   点击服务器->连接
*   在“收藏夹”下，右键单击您的服务器名称，然后单击“编辑”
*   删除用户名，并在那里写超级用户(正如我写的)
*   写入超级用户密码
*   点击“确定”
*   点击“连接”

您是超级用户。但是超级用户没有发言权。所以给你之前的用户所有的超级用户能力和语音能力。

===作为以前的用户连接===

*   点击服务器->连接
*   在“收藏夹”下，右键单击您的服务器名称，然后单击“编辑”
*   删除密码
*   删除超级用户名称，并在那里写入以前的用户名
*   点击“确定”
*   点击“连接”

==公开您的服务器==

===创建证书颁发机构(CA)===

您不必再执行以下操作。服务器将注册为公共使用杂音证书。如果您想使用自己的证书颁发机构，可以执行这些操作。

*   创建工作目录

    ```
     mkdir CAwork
        cd CAwork 
    ```

*   首先将自己设置为 CA。CA 可以签署证书请求以创建 SSL 证书。

*   运行命令(将在 CAwork 下创建一个目录 demoCA)

    ```
     /etc/ssl/misc/CA.pl -newca 
    ```

*   按 enter 键创建 CA 证书

*   输入 PEM 密码短语

*   输入通行短语

*   填写要求的信息。确保设置所有字段(例如)

    ```
     countryName  = GR
          stateOrProvinceName    = Some-State
          organizationName       = Internet Widgits Pty Ltd
          commonName             = ariadni.noip.me CA
          emailAddress           = webmaster@ariadni.noip.me 
    ```

*   确保公用名不同于下一步中的公用名。

CA.pl 将在目录 demoCA 中创建一个 cacert.pem，私有 CA 密钥在 private/cakey.pem 中

===创建证书请求===
接下来，我们将创建一个服务器证书请求。

```
 /etc/ssl/misc/CA.pl   -newreq-nodes 
```

Enter fullscreen mode Exit fullscreen mode

一些服务器需要不受密码保护的私有证书。-nodes(注意:newreq 和-nodes 之间没有空格)告诉 openssl 创建一个不受密码保护的证书。
确保将通用名称设置为将要使用它的服务器的 dns 名称！

确保设置所有字段(例如)

```
 countryName  = GR
            stateOrProvinceName  = Some-State
            organizationName     = Internet Widgits Pty Ltd
            commonName           = ariadni.noip.me
            emailAddress         = webmaster@ariadni.noip.me 
```

Enter fullscreen mode Exit fullscreen mode

*   确保公用名不同于上一步中的公用名。例如，我将 ariadni.noip.me 命名为 CA 和 ariadni.noip.me

请求文件将在您当前所在的目录中创建，名称为 newreq.pem，私钥在 newkey.pem 中

===签署请求= = =

接下来使用您的 CA 证书签署证书请求:

```
 /etc/ssl/misc/CA.pl   -sign 
```

Enter fullscreen mode Exit fullscreen mode

将提示您输入 CA 密码。将显示 newreq.pem 文件中的信息，并询问您是否要签名。签署证书后，系统会提示您是否要提交证书。

证书被创建、签名并存储在 newcert.pem 中，该目录与您当前所在的目录相同。

现在更多的工作，我们准备好了。
证书在同一个文件中既有编码版本又有人类可读版本。您可以像下面这样去掉人类可读的部分:

```
 cp demoCA/cacert.pem tmp.pem
          openssl x509 -in tmp.pem -out cert.pem 
```

Enter fullscreen mode Exit fullscreen mode

cert.pem 是我们的根证书

```
 mv newcert.pem tmp.pem
          openssl x509 -in tmp.pem -out newcert.pem 
```

Enter fullscreen mode Exit fullscreen mode

newcert.pem 是我们从证书颁发机构请求的签名文件

===安装证书和密钥===
创建一个包含根证书颁发机构证书(cert.pem)和签名证书文件(newcert.pem)内容的文件 mycert.pem

```
 cat cert.pem newcert.pem > mycert.pem
          mv newkey.pem mykey.pem 
```

Enter fullscreen mode Exit fullscreen mode

在这一步之后，您有两个文件:

*   mykey.pem 中的私钥
*   组合的根证书颁发机构证书和您在 mycert.pem 中签名的证书

现在，文件 mycert.pem 包含证书，mykey.pem 包含密钥。

*   将这两个文件复制到 brunh . ini 所在的/etc/
*   编辑/etc/burn . ini 取消注释并更改以下两行:

    ```
     sslCert=mycert.pem
             sslKey=mykey.pem 
    ```

===重启杂音= = =

```
 /etc/rc.d/rc.murmur restart 
```

Enter fullscreen mode Exit fullscreen mode

告诉你的朋友关于你的咕哝服务器，他们可以在你的国旗下找到你。无论如何，给他们你的 dyndns.org 域名，把它添加到“收藏夹”(见上)。
并能与你连接和交谈，即使你的服务器不出现在公共互联网服务器列表中。