# NextCloud:转移所有权

> 原文：<https://dev.to/nabbisen/nextcloud-transfer-ownership-5hhj>

**封面图片为[正式发布的](https://nextcloud.com/press/)。*

## 总结

[NextCloud](https://nextcloud.com/) 是从 [ownCloud](https://owncloud.org/) 分叉出来的开源自托管文件共享平台。

由于前几天的人事变动，我不得不转让共享文件/目录的所有权。

一个简单的方法是下载所有的文件，然后由新的所有者上传。但是这太不切实际了，因为它们太大了。

或者，我用 [`occ`](https://docs.nextcloud.com/server/latest/admin_manual/configuration_server/occ_command.html) 通过命令行来实现。
`occ`是 NextCloud 执行服务器操作的命令行接口，其名称在 ["o"wn"C"loud(原点)" C"onsole](https://doc.owncloud.com/server/admin_manual/configuration/server/occ_command.html) 之后。

## 环境

*   OS: [厘斯](https://www.centos.org/) 6
*   网页:[阿帕奇](https://www.apache.org/)的 [httpd](https://httpd.apache.org/) 2
*   APP: [PHP](https://www.php.net/) 7.3
*   文件共享平台:NextCloud 16

## 教程

`occ`使用的命令是 [`Files`](https://docs.nextcloud.com/server/latest/admin_manual/configuration_server/occ_command.html#file-operations) ' [`transfer-ownership`](https://docs.nextcloud.com/server/latest/admin_manual/configuration_server/occ_command.html#file-operations)
基本格式是:

```
$  occ files:transfer-ownership <from-user> <to-user> 
```

Enter fullscreen mode Exit fullscreen mode

在我的环境中，`occ`并不存在于 [`PATH`](https://en.wikipedia.org/wiki/PATH_(variable)) 环境变量之中。
因此，我用 [`find`](https://en.wikipedia.org/wiki/Find_(Unix)) 来获取路径，并在命令行中指定为`<lib-dir-path>/occ`。另外，我必须用指定的 PHP 运行它。

```
$  php73 <lib-dir-path>/occ files:transfer-ownership <from-user> <to-user> 
```

Enter fullscreen mode Exit fullscreen mode

最后，它需要作为 web 用户运行，在我的例子中是`apache`:

```
$  sudo -u apache php73 <lib-dir-path>/occ files:transfer-ownership <from-user> <to-user> 
```

Enter fullscreen mode Exit fullscreen mode

输出为:

```
Analysing files of <from-user>  ...
 16442 [============================] Collecting all share information for files and folder of <from-user>  ...
 25 [============================] Transferring files to <to-user>/files/transferred from <from-user> on 2019-06-12 01-56-02 ...
Restoring shares ...
Share with id 4 points at deleted file, skipping
 1/25 [=>--------------------------]   4%Share with id 11 points at deleted file, skipping
 2/25 [==>-------------------------]   8%Share with id 12 points at deleted file, skipping
 3/25 [===>------------------------]  12%Share with id 16 points at deleted file, skipping
 7/25 [=======>--------------------]  28%Share with id 1 points at deleted file, skipping
 8/25 [========>-------------------]  32%Share with id 9 points at deleted file, skipping
 13/25 [==============>-------------]  52%Share with id 90 points at deleted file, skipping
 14/25 [===============>------------]  56%Share with id 91 points at deleted file, skipping
 15/25 [================>-----------]  60%Share with id 28 points at deleted file, skipping
 19/25 [=====================>------]  76%Share with id 57 points at deleted file, skipping
 20/25 [======================>-----]  80%Share with id 58 points at deleted file, skipping
 21/25 [=======================>----]  84%Share with id 60 points at deleted file, skipping
 22/25 [========================>---]  88%Share with id 61 points at deleted file, skipping
 23/25 [=========================>--]  92%Share with id 87 points at deleted file, skipping
 24/25 [==========================>-]  96%Share with id 88 points at deleted file, skipping
 25/25 [============================] 100% 
```

Enter fullscreen mode Exit fullscreen mode

于是，**在<到用户>的家**创建了一个名为“2019-06-12 01-56-02 从<从用户>转移过来”的新目录(打印在控制台中)，所有文件归那里的新主人所有。
那时我所要做的就是以< to-user >的身份登录，并根据需要移动它们: )

* * *

## 参考文献

*   NextCloud 的[“将文件传输给另一个用户”](https://docs.nextcloud.com/server/latest/admin_manual/configuration_files/file_sharing_configuration.html#transferring-files-to-another-user)
*   其他命令
    *   `occ list`列出命令。
    *   `occ help <command>`显示命令的帮助。比如`occ help files`会显示子命令: [`cleanup`](https://docs.nextcloud.com/server/latest/admin_manual/configuration_server/occ_command.html#cleanup) 、 [`scan`](https://docs.nextcloud.com/server/latest/admin_manual/configuration_server/occ_command.html#scan) 、`transfer-ownership`。

<center>🌱 🦉 🌿 🦔 🌳</center>

感谢您的阅读。祝上菜愉快。