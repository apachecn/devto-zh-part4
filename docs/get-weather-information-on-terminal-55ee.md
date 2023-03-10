# 在终端上获取天气信息

> 原文：<https://dev.to/anuragrana/get-weather-information-on-terminal-55ee>

你可以在你的终端上获得你所在地区的天气信息。

您需要在 URL `wttr.in/$1?m1`
上发送 curl 请求

```
curl -s "wttr.in/$1?m1" 
```

您可以将此添加到您的。bashrc 文件作为命令。

```
weather(){ curl -s "wttr.in/$1?m1"} 
```

获取 3 天的信息:`curl -s "wttr.in"`

更多来自作者:

*   [如何跟踪 Django 应用程序发送的邮件打开](https://www.pythoncircle.com/post/626/how-to-track-email-opens-sent-from-django-app/)
*   [如何在 Django 下载大型 csv 文件](https://www.pythoncircle.com/post/677/how-to-download-large-csv-files-in-django/)
*   [在 Django 应用程序中记录数据库变化](https://www.pythoncircle.com/post/675/logging-databases-changes-in-django-application/)