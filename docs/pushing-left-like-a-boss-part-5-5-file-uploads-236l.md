# 像老板一样向左推—第 5.5 部分—文件上传

> 原文：<https://dev.to/azure/pushing-left-like-a-boss-part-5-5-file-uploads-236l>

### [这个系列，还有我的博客，都感动了！](https://wehackpurple.com/pushing-left-like-a-boss-part-5-5-file-uploads/)

允许文件上传到您的应用程序(以及您的网络)是一件危险的事情。事实上，它可能是你可以添加到 web 应用程序中的最危险的功能。

如果您决定在应用程序中包含文件上传，您应该:

1.  用应用程序扫描所有上传的文件，分析文件的恶意特征，如[组装线](https://www.cse-cst.gc.ca/en/assemblyline)(由加拿大政府免费提供，可以在本地安装，因此您不需要与第三方共享您的文件) [Cylance](https://www.cylance.com/en-us/index.html) 、[火眼](https://www.fireeye.com/)或[病毒总数](https://www.virustotal.com/)。
2.  遵循 [OWASP 文件中的建议上传备忘单](https://www.owasp.org/index.php/Input_Validation_Cheat_Sheet#File_Upload_Validation)。
3.  与 [Dominique Righetto](https://twitter.com/righettod) 一起观看 OWASP DevSlop 节目的第 14 集，查看代码和更多关于如何实施这些安全措施的内容。这一集是法语的，配有英语字幕(大部分集是用英语录制的)。既然你这么做了，为什么不[订阅我们的 YouTube 频道](https://aka.ms/DevSlopshow)？如果你喜欢这个博客，你可能也会喜欢这个节目。

[![Start Security Earlier! SecTor 2018](img/9238d4ce25c5f80c2ef096ae396f1f30.png "Start Security Earlier! SecTor 2018")](https://res.cloudinary.com/practicaldev/image/fetch/s--oNDRbl4m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cu5kl4e2o7z09xuw3bbx.jpeg)

###### 早点启动安全！部门 2018

下面是一些要点，总结自[戴夫·威彻斯](https://twitter.com/wichers) :
写的 [OWASP 文件上传备忘单](https://www.owasp.org/index.php/Input_Validation_Cheat_Sheet#File_Upload_Validation)，确保应用程序...

### [在我的新博客上阅读这篇文章的其余部分！](https://wehackpurple.com/pushing-left-like-a-boss-part-5-5-file-uploads/)