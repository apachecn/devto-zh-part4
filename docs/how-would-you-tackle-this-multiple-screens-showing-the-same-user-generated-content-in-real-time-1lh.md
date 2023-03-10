# 你会如何解决这个问题？多个屏幕实时显示同一用户生成的内容。

> 原文：<https://dev.to/nataliedeweerd/how-would-you-tackle-this-multiple-screens-showing-the-same-user-generated-content-in-real-time-1lh>

# 问题

因此，我最近有一个项目，其中有两个组成部分:“应用程序”和“大屏幕”。用户用应用程序拍下他们的照片，应用一些有趣的效果，然后发送到大屏幕上让全世界看到！这些“大屏幕”一直开着。

“应用程序”是由另一个外部开发人员开发的。他们会通过一个自定义的 API 将数据传递给我，并显示在大屏幕上。屏幕本身是一个超级简单的微型网站，它通过几个图像旋转，直到它被一些从应用程序传递给我的用户生成的内容打断。

然而，问题是这些应用程序和屏幕可以安装在多个位置。无论照片上传到哪里，它都应该出现在每个屏幕上。因此，如果有人在新加坡拍了一张照片，它就会出现在伦敦和新加坡的大屏幕上。

**基本用户旅程**

1.  用户使用应用程序拍照。
2.  用户添加了一些有趣的效果。
3.  用户将照片发送到大屏幕。
4.  世界各地的大屏幕都在播放这张照片。
5.  照片然后被安排删除。

# 我的解决方案

最初，我只使用了一个 AJAX 调用来获取数据库中存储的下一张照片，然后将其设置为“used ”,这样它就不会再出现了。然而，这意味着如果屏幕 A 在屏幕 B 之前抓住它，屏幕 B 将永远看不到它。如果用户在屏幕 B 前等着看自己，那就不理想了！

所以我选择了服务器端 CRON 路线。每 10 秒钟，我的 CRON 就会运行一次，并将任何当前图像的状态从 2 设置为 1；并将任何未使用的图像设置为 2。下面是我用来每 10 秒运行一个命令的 CRON 脚本。

```
for i in {1..5}; do curl --silent "https://***/api/prep" &>/dev/null; sleep 10; done 
```

我的数据库中的表如下所示:

| 身份证明 | img | 状态 |
| --- | --- | --- |
| one | e1321fsdffdsfds.jpg | one |
| Two | mpfhmnh80fhj823.jpg | Two |
| three | fdsf 高 8h1u.jpg | Zero |

0 代表一个还没有被使用的图像，实际上是在排队。
1 代表已经在大屏幕上使用/显示的图像。
2 表示屏幕上要显示的当前图像。

我的 AJAX 调用将获取下一张图片并显示它。

这大部分是有效的；然而，图像不出现在屏幕上的可能性很小。我的 CRON 每 10 秒运行一次。我的 Javascript AJAX 调用每 10.15 秒运行一次。用户拍摄的图像极有可能不会出现在他们正在看的屏幕上。

# 你会如何解决这个问题？

非常想听听其他开发者是如何解决这个问题的！我上面介绍的解决方案是一个基于 PHP 的解决方案，加入了一些 AJAX/JS。

**我应该加上这个项目成功启动。我只是希望打开一个讨论，以发现什么其他技术/方法可以用来完成这项工作。**