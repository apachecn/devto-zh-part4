# Jackson 反序列化漏洞

> 原文：<https://dev.to/brianverm/jackson-deserialization-vulnerability-3bpe>

2019 年 7 月 29 日公布了一个影响 com . faster XML . Jackson . core:Jackson-databind up to 2.9.9.2 所有版本的高严重性[不可信数据反序列化](https://snyk.io/vuln/SNYK-JAVA-COMFASTERXMLJACKSONCORE-455617)漏洞( [CVE-2019-14379](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-14379) 、 [CVE-2019-14439](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-14439) )。对于那些使用 Spring Boot 的人，请注意当前版本(2.1.7)依赖于较早的易受攻击的 jackson-databind 2.9.9 软件包。

## 关于漏洞

这不是 jackson-databind 包第一次受到不可信数据漏洞的反序列化。事实上，自 2018 年以来，已知和披露的这些漏洞有十几个，更不用说几乎所有这些漏洞都被认为是高度严重的。

该库的主要目标是将 Java 对象序列化为 JSON，反之亦然。问题在于将 JSON 反序列化回 Java 对象。当一个编写得很差的 Java 应用程序能够反序列化来自不可信来源的 JSON 字符串时，黑客可以利用这一点，例如，启动远程代码执行。

如果为 Jackson 激活了多态类型，攻击者就可以在控制 JSON 输入时使用一个小工具来发起攻击。小工具是在易受攻击的进程中已经存在可执行代码的类或函数。这种现有的可执行代码可以被恶意重用。

对于此特定漏洞，当启用 Jackson 中的默认输入时，Ehcache 中的小工具可能会被误用。jackson-databind 的维护者通过将特定的小工具添加到已经存在的黑名单中，很快解决了这个问题。2.9.9.3 版本已发布，不再包含此漏洞。

## Spring Boot

在写这篇博客的时候，Spring Boot 的最新发布版本是 2.1.7。这个版本依赖于旧的 jackson-databind 2.9.9 包。

[阅读有关如何解决此问题的更多信息...](https://snyk.io/blog/jackson-deserialization-vulnerability/)