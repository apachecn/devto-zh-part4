# 在 Squid 服务中配置 SSL 碰撞

> 原文：<https://dev.to/suntong/configuring-ssl-bumping-in-the-squid-service-2e7h>

*转贴自[https://help.kaspersky.com/KWTS/6.0/en-us/166244.htm](https://help.kaspersky.com/KWTS/6.0/en-us/166244.htm)，最近一次评论是在 2019 年 1 月 18 日*

要在 Squid 服务中配置 SSL 碰撞:

1.  确保所使用的 Squid 服务支持必要的选项。为此，执行命令:

    `squid -v`

    【配置选项】参数必须包含`--enable-ssl-crtd`和`--with-openssl`值。

2.  转到 Squid 服务文件夹。为此，执行命令:

    `cd /etc/squid`

3.  创建自签名 SSL 证书。为此，执行命令:

    `openssl req -new -newkey rsa:2048 -days <certificate validity period in days> -nodes -x509 -keyout squidCA.pem -out squidCA.pem`

    将提示您填写自签名 SSL 证书的字段。

4.  请填写自签名 SSL 证书的字段。

5.  创建要导入浏览器的可信证书。为此，执行命令:

    `openssl x509 -in squidCA.pem -outform DER -out squid.der`

6.  将 squid.der 文件导入到本地计算机用户的浏览器中。
    将 squid.der 文件导入浏览器的方法取决于浏览器的类型。

7.  配置权限以使用自签名证书文件。为此，根据所用的操作系统运行以下命令...

    *。。。细节省略。对于这一步和接下来的两步，即步骤 7、8、9，请参考原始文章。。。*

最后，重启 Squid 服务。为此，执行命令:

`service squid restart`

Squid 服务中的 SSL 缓冲配置将完成。