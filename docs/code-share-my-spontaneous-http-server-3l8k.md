# 代码共享:我自发的 HTTP 服务器

> 原文：<https://dev.to/hoffmann/code-share-my-spontaneous-http-server-3l8k>

(是的，我知道，VSCode 包括精彩的 [Live 服务器](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer))

这是我的胶带和喷雾泡沫解决方案，让你开始你的 HTTP 服务器沙发土豆风格:

成分:

*   壳，
*   顶点
*   netstat
*   坞站(trintronx/python-simple http server)
*   pwgen

方向:

1.  询问目录
2.  检查当前使用的端口
3.  随机选择一个好的自由港
4.  生成唯一的容器名
5.  通过 HTTP 服务器提供目录服务
6.  显示带有关闭服务器选项的地址链接

查看[源](https://github.com/HoffmannP/Spontaneous-HTTP-Server)