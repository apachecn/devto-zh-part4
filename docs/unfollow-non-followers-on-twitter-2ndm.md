# 取消关注 Twitter 上的非关注者

> 原文：<https://dev.to/jalbam/unfollow-non-followers-on-twitter-2ndm>

这里有一段 JavaScript 代码，可以停止关注那些没有在 Twitter 上关注你的人，并保留那些你想要的人:[https://gist . github . com/jalbam/d 7678 c 32 b 6 f 029 c 602 c 0 bfb 2 a 72 e0c 26](https://gist.github.com/jalbam/d7678c32b6f029c602c0bfb2a72e0c26)

这将适用于新的 Twitter 网站代码结构(从 2019 年 7 月开始更改，导致其他 unfollow-scripts 停止工作)。

说明:

1.  代码可能需要根据您的 Twitter 网站的语言进行修改:
    *   对于英文网站，不需要修改。
    *   对于西班牙语网站，请记住将变量“语言”设置为“ES”。
    *   对于另一种语言，请记住将变量“language”设置为该语言，并修改“WORDS”对象以添加该语言的单词。
2.  或者，您可以编辑“SKIP_USERS”数组，以插入您不想取消关注的用户(即使他们没有关注您)。
3.  代码完成后，在 Twitter 网站上，转到显示您关注的所有人的部分(*[【https://twitter.com/YOUR\_USERNAME\_HERE/following】](https://twitter.com/YOUR%5C_USERNAME%5C_HERE/following)*)。
4.  在那里，打开 JavaScript 控制台(通常是 F12 键)，粘贴所有代码并按 enter。
5.  等到你看到它已经完成。如果出现问题，重新加载页面并再次从第 3 步开始重复。

注意:**仍然不确定英语确认按钮中的文本。有需要的话告诉我或者自己修改到正确的！**

Joan Alba Maldonado 的 gist:[https://gist . github . com/jalbam/d 7678 c 32 b 6 f 029 c 602 c 0 bfb 2 a 72 E0 c 26](https://gist.github.com/jalbam/d7678c32b6f029c602c0bfb2a72e0c26)

谢谢您们。欢迎任何评论。