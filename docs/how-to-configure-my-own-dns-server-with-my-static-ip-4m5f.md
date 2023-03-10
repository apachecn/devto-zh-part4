# 如何用我的静态 ip 配置我自己的 dns 服务器

> 原文：<https://dev.to/djemos/how-to-configure-my-own-dns-server-with-my-static-ip-4m5f>

因为一个朋友要用静态 ip 设置自己的 dns 服务器，并在服务器上安装了 ubuntu，
所以我设置了 dns 服务器。
以下说明适用于任何可能需要这样做的情况。
这里的域名是 my-domain.gr 你用自己的替换。

以下规定适用于 Ubuntu，在 slackware、slackel、salix 各自的文件中也是如此。这些说明很简单，而且没有正确的技术词汇，无法让所有人理解。
以下说明有效，当然其他人可能会使用不同的设置。

假设在注册服务商上，我们维护我们的域名，我们已经创建了一个名称服务器，例如 ns0.my-domain.gr，为其提供了一个静态 ip x1.x2.x3.x4
我们可以使用相同的静态 ip ns1.my-domain.gr
定义第二个名称服务器，然后在示例 my-domain.gr 中，我们将 ns0.my-domain.gr 和 ns1.my-domain.gr 作为域名服务器(名称服务器)

此外，域名服务器可能需要几个小时到 48 个小时来更新与我们的 ns0.my-domain.gr.
对应的 ip 地址，因此，如果您更改了您的域名服务器，将在大约 3 到 48 个小时内完成到新托管服务器的过渡，这取决于为您提供互联网连接的公司。
所以，如果你不首先更新世界各地的域名服务器，从任何一台电脑上进行搜索，你都找不到这个域名。

以下是我们在自己的服务器上做的，如果你从家里运行，有静态 ip，或者在登录到我们在这样一个公司的服务器后，使用 ssh。在这里，我们以域名 my-domain.gr 为例，你可以先注册你自己的域名。
静态 ip 是 x1 . x2 . x3 . x4
x1 . x2 . x3 . x4 换成你的静态 ip
公司名称服务器的 ip 是 k1.k2.k3.k4 m1.m2.m3.m4

下面哪里是 x1，x2，x3，x4，k1，k2，k3，k4，m1，m2，m3，m4 会放相应的数字。

= =/etc/bind/named . conf . local = =
' ' ' a . ' ' ' file/etc/bind/named . conf . local 应为:
对于 slackware，该文件为/etc/named.conf

```
zone "my-domain.gr." IN {
  type master;
 file "/etc/bind/db.my-domain.gr"; // for slackware "/var/named/caching-example/db.my-domain.gr"
 };

  zone "x4.x3.x2.in-addr.arpa" {
  type master;
  file "/etc/bind/rev.x4.x3.x2.in-addr.arpa"; // for slackware "/var/named/caching-example/rev.x4.x3.x2.in-addr. arpa "
  }; 
```

= =/etc/bind/named.conf.options = =
' ' ' b . ' ' '文件/etc/bind/named . conf . options 应该是:

对于 slackware 把它们放在上面的文件/etc/named.conf 中

```
 options {
    directory "/var/cache/bind"; // for slackware directory "/var/named";
    notify no;
    forward only;
    forwarders {k1.k2.k3.k4; m1.m2.m3.m4;}; // here we put our name servers that has given the company that runs the server.
    }; 
```

== /etc/resolv.conf ==
'''C . ' ' '文件/etc/resolv.conf
对于 slackware 同样如此

```
search my-domain.gr 
nameserver x1.x2.x3.x4
nameserver k1.k2.k3.k4
nameserver m1.m2.m3.m4 
```

== /etc/bind /db.my-domain.gr ==

' ' ' d . ' ' '文件/etc/bind /db.my-domain.gr 应为:
在 slackware 上，该文件为“/var/named/caching-example/db . my-domain . gr”

```
;
$ TTL 604800
@ IN SOA ns0.my-domain.gr. my-domain.gr. (
                    2006081401
                     28800
                     3600
                     604800
                     38400
 )
                    IN NS ns0
                    IN NS ns1
 my-domain.gr. IN A x1.x2.x3.x4
 my-domain.gr. IN MX 10 mail.my-domain.gr.
 ns0 IN A x1.x2.x3.x4
 ns1 IN A x1.x2.x3.x4
 www IN A x1.x2.x3.x4
 mail IN A x1.x2.x3.x4 
```

= =/etc/bind/rev . x4 . x3 . x2 . in-addr . arpa = =

' ' ' e . ' ' '文件/etc/bind/rev . x4 . x3 . x2 . in-addr . arpa 应为:
在 slackware 上文件为“/var/named/caching-example/rev . x4 . x3 . x2 . in-addr . arpa”

```
$ TTL 1D 
@ IN SOA ns0.my-domain.gr. admin.my-domain.gr. (
                    2006081401 ;
                    28800 ;
                    604800 ;
                    604800 ;
                    86400
)
IN NS ns0.my-domain.gr.
x4 IN PTR my-domain.gr 
```

x4 与静态 ip 的最后一个数字(x1.x2.x3.x4)相同

==测试服务器==
'''F . ' ' '测试服务器

named-check zone-d x4 . x3 . x2 . In-addr . arpa/etc/bind/rev . x4 . x3 . x2 . In-addr . arpa//在 slackware 中，该文件为“/var/named/caching-example/rev . x4 . x3 . x2 . In-addr . arpa”

```
 OUTPUT:
  loading "x4.x3.x2.in-addr.arpa" from "/etc/bind/rev.x4.x3.x2.in-addr.arpa" class "IN"
  zone x4.x3.x2.in-addr.arpa/IN: loaded serial 2006081401
  OK 
```

named-check zone-d my-domain . gr/etc/bind/db . my-domain . gr//在 slackware 中，该文件是“/var/named/caching-example/db . my-domain . gr”

```
 OUTPUT:
  loading "my-domain.gr" from "/etc/bind/db.my-domain.gr" class "IN"
  zone my-domain.gr/IN: loaded serial 2006081401
  OK 
```

= = restart bind = =
' ' ' g . ' ' '我们重新启动 bind
/etc/init . d/bind 9 restart
对于 slackware/etc/RC . d/RC . bind restart 或者对于 slackel，Salix " service restart bind "
ssh shell 中的以下命令应该给我们(而不是 x1.x2.x3.x4)输入您的静态 ip 的编号。

djemos [~] $ nslookup my-domain.gr

```
 Server: 192.168.1.254
  Address: 192.168.1.254 # 53
  Non-authoritative answer:
  Name: my-domain.gr
  Address: x1.x2.x3.x4 
```

dje mos[~]$ nslookup[www . my-domain . gr](http://www.my-domain.gr)

```
 Server: 192.168.1.254
 Address: 192.168.1.254 # 53
 Non-authoritative answer:
 Name: www.my-domain.gr
 Address: x1.x2.x3.x4 
```

dje mos[~]$ nslookup mail . my-domain . gr

```
 Server: 192.168.1.254
 Address: 192.168.1.254 # 53
 Non-authoritative answer:
 Name: mail.my-domain.gr
 Address: x1.x2.x3.x4 
```

dje mos[~]$ dig[www . my-domain . gr](http://www.my-domain.gr)

```
 ; << >> DiG 9.7.0-P1 << >> www.my-domain.gr
 ;; Global options: + cmd
 ;; Got answer:
 ;; - >> HEADER << - opcode: QUERY, status: NOERROR, id: 29213
 ;; Flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 2, ADDITIONAL: 2
 ;; QUESTION SECTION:
 ; Www.my-domain.gr. IN A
 ;; ANSWER SECTION:
 www.my-domain.gr. 604800 IN A x1.x2.x3.x4
 ;; AUTHORITY SECTION:
 my-domain.gr. 604800 IN NS ns0.my-domain.gr.
 my-domain.gr. 604800 IN NS ns1.my-domain.gr.
;; ADDITIONAL SECTION:
ns0.my-domain.gr. 604800 IN A x1.x2.x3.x4
ns1.my-domain.gr. 604800 IN A x1.x2.x3.x4
;; Query time: 8 msec
;; SERVER: x1.x2.x3.x4 # 53 (x1.x2.x3.x4)
;; WHEN: Fri Jun 1 09:46:43 2012
;; MSG SIZE rcvd: 120 
```