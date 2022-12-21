# 使用 Groovy 和身份验证下载文件

> 原文：<https://dev.to/thokuest/downloading-files-with-groovy-and-authentication-6bb>

用 Groovy 从一个需要用户认证的 URL 下载文件就像:
一样简单

```
java.net.Authenticator.setDefault (new Authenticator() {
    protected PasswordAuthentication getPasswordAuthentication() {
        return new PasswordAuthentication(username, password.toCharArray());
    }
});

destinationFile.withOutputStream { it << new URL(downloadUrl).newInputStream() } 
```

**链接**

*   Stackoverflow: [groovy -下载带认证的文件](https://stackoverflow.com/questions/50062880/groovy-download-file-with-authentication)
*   Stackoverflow: [使用 Java](https://stackoverflow.com/questions/496651/connecting-to-remote-url-which-requires-authentication-using-java/496707#496707) 连接到需要认证的远程 URL