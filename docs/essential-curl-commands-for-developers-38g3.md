# 开发人员的基本 cURL 命令

> 原文：<https://dev.to/andremare/essential-curl-commands-for-developers-38g3>

这篇文章提供了我作为 Java 开发人员使用的 cURL 命令的快速参考。这并不是一套完整的 cURL 命令，对每个命令的作用都有详细的解释。如果您想阅读关于 cURL 命令的不同选项和参数的详细文档，请参考 cURL 命令的*手册*页。

此外，有许多 REST 客户端和 ide，如 Eclipse 和 IntelliJ，允许您调用对服务器的 HTTP 请求，但是我发现自己不得不在调试过程中从命令行调用这些请求。打开终端并使用这些命令来调用命令也比在 IDE 或其他 UI 应用程序中为不同类型的请求设置配置要快。

使用此参考来学习 cURL 命令，并将其作为一个重要工具添加到您的技能组合中。

#### 帮助和信息命令

要查找帮助或了解有关 curl 命令中使用的不同选项和参数的更多信息，您可以在终端中键入以下任意内容。

```
# Display quick help information about the curl command
$ curl --help

# Quick search for specific curl options or arguments
$ curl --help | grep {search_value}

# Display the manual page for the curl command
$ man curl

# Displays information about curl and the libcurl version it uses.
$ curl --version
$ curl -V 
```

Enter fullscreen mode Exit fullscreen mode

#### 通用

本节包含基本的 curl 命令，您可以在这些命令上添加其他部分列出的更多选项和参数。

```
# Retrieve content of URL and display it on screen
$ curl {URL}

# Write output to a file instead of stdout.
$ curl {URL} > {filename}
$ curl -o {filename} {URL}

# Write output to a local file named like the remote file we get
$ curl -O {URL}

# Enables verbose detail to be displayed
$ curl -v {URL}

# Enables a full trace dump on all incoming and outgoing data.
$ curl --trace {filename} {URL}

# Silent Mode: Don’t show progress meter or error messages.
$ curl -s {URL} 
```

Enter fullscreen mode Exit fullscreen mode

#### HTTP 头

HTTP 头是超文本传输协议(HTTP)消息头的请求和响应消息中显示的名称或值对。这些命令将帮助您设置和读取 HTTP 头。

```
# Fetch the headers from the server (HEAD)
$ curl -I {URL}

# Include the HTTP-header in the output
$ curl -i {URL}

# Pass multiple headers as part of the request to the server.
$ curl -H "{HTTP General Header}" -H "{HTTP Request Header}" {URL}

# Include custom HTTP Header in the request to the server.
$ curl -H "{X-Key}:{Value}" {URL}

# Write the received protocol headers to the specified file.
$ curl -D {filename} {URL} 
```

Enter fullscreen mode Exit fullscreen mode

#### HTTP cookie

HTTP cookie 是从网站发送的一小段数据，在用户浏览时由用户的 web 浏览器存储在用户的计算机上。这些命令将帮助您设置和读取 HTTP Cookies。

```
# Write all cookies to filename after completed command.
$ curl -c {filename} {URL}

# Read cookies from file and pass data to HTTP Server in Cookie header. 
$ curl -b {filename} {URL}

# Read cookies from data (key:value) and pass to HTTP Server in Cookie header.
$ curl -b "{key1:value1;key2:value2}" {URL}

# Discard all "session cookies". New session started.
$ curl -j {URL} 
```

Enter fullscreen mode Exit fullscreen mode

#### 代理

代理服务器是一种服务器，充当从其他服务器寻求资源的客户端请求的中介。这些命令将有助于设置代理服务器的 URL 和端口号，以及身份验证。

```
# Use the specified proxy server and port number
$ curl -x {proxy_url}:{port} {URL}

# Specify the user name and password to use for proxy authentication.
$ curl -U {username}:{password} - x {proxy_url}:{port} {URL}

# Comma-separated list of hosts which do not use a proxy
$ curl --noproxy {url1} -x {proxy_url}:{port} {URL} 
```

Enter fullscreen mode Exit fullscreen mode

#### FTP 服务器

文件传输协议(FTP)是一种标准网络协议，用于在计算机网络上的客户端和服务器之间传输计算机文件。这些命令将有助于在 FTP 服务器之间传输文件。

```
# Display a directory listing of an FTP site (Anonymous)
$ curl {FTP_URL}

# Retrieve a file from a FTP site. (Anonymous)
$ curl {FTP_URL}/{filename} 

# Upload a file to a FTP site. (Anonymous)
$ curl -T {filename} {FTP_URL}

# Upload Files to an FTP Server. (Secured)
$ curl -u {ftpuser}:{ftppass} -T {filename} {FTP_URL}

# Upload multiple files to an FTP Server
$ curl -u {ftpuser}:{ftppass} -T "{file1,file2}" {FTP_URL}

# Upload a file to a FTP site and append to an existing file
$ curl -T {filename} -a {FTP_URL}/{filename} 
```

Enter fullscreen mode Exit fullscreen mode

#### HTTP Post/Put 数据

HTTP POST 和 PUT 请求方法请求 web 服务器接受请求消息体中包含的数据，很可能是为了存储数据。这些命令将向您展示如何将数据作为请求的一部分。

```
# Send data in a POST/PUT request to the HTTP server
$ curl -d '{key1=value1&key2=value2}' -X {POST/PUT} {URL}

# Send data from a file in a POST/PUT request to the HTTP server
$ curl -d {@filename} -X {Post/PUT} {URL} 
```

Enter fullscreen mode Exit fullscreen mode

#### 休息 API

RESTful API 是一个应用程序编程接口(API)，它使用 HTTP 请求来获取、上传、发布和删除数据。以下命令说明了如何用不同的 HTTP 方法调用 REST APIs。

```
# Invoke REST API (GET & JSON)
$ curl -i -H "Content-Type: application/json" -X GET {REST_API}

# Invoke REST API (POST & JSON)
$ curl -i -H "Content-Type: application/json" -X POST -d '{key:value}' {REST_API}

# Invoke REST API (PUT & JSON)
$ curl -i -H "Content-Type: application/json" -X PUT -d '{key:value}' {REST_API}

# Invoke REST API (POST)
$ curl -X DELETE {REST_API}/{ID} 
```

Enter fullscreen mode Exit fullscreen mode

#### 安全

有很多方法可以通过服务器验证 curl 命令。以下是执行身份验证的一些方法。

```
# Authenticate with username and password
$ curl -u {username}:{password} {URL}

# Authenticate with SSL certificate and password
$ curl --cert {cert_file.pem}:{password} {URL} 
```

Enter fullscreen mode Exit fullscreen mode

#### 配置文件

curl 配置文件被命名为。curlrc (Linux)或 _curlrc (Windows ),可以在用户的主目录中找到。

文件和条目的示例如下:

```
# The default Proxy and port to use
proxy = 127.0.0.1:8080
# The proxy username and password
proxy-user = "user:pass"
# The timeout is set to 60 seconds
-m 60 
```

Enter fullscreen mode Exit fullscreen mode

#### 总结

本文中列出的 curl 命令绝不是一套完整的 curl 命令，只是作为开发人员使用的快速参考。要查看 curl 命令的完整文档，请阅读 curl 命令的*手册*页。

在任何不同的社交媒体平台上关注我，并随时留下评论。