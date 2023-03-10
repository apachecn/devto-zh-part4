# 黑盒子邀请代码挑战！是时候开始破解了。

> 原文：<https://dev.to/powerexploit/hack-the-box-invite-code-challenge-its-time-to-begin-hack-34a6>

这是对所有开发者和程序员的公开挑战&黑客破解了 Hackthebox 的邀请代码，在评论框里玩这个解释你的工具&技巧~谢谢。

**Hackthebox** *是一个在线平台，用于测试和提升您在渗透测试和网络安全方面的技能。立即加入我们的在线实验室，开始培训。如果你想学习关于**pentest，hacking** 的矿石，那么 **hackthebox** 是所有初学者最好的在线平台之一。但是等等，让我们来谈谈有趣的部分，首先你需要黑掉这个盒子，也就是说你需要得到这个超级平台的邀请代码。
[![Alt Text](img/30fe2912d9e48923102d4da35ecc4e56.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VQYoYyXk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6k48fwa5bm6r594d1m1u.JPG)

我在下面解释了我的挑战方法！

## **我的工具和窍门破解 hackthebox 的邀请码:**

### 我的第一个方法:

->工具:

*   chrome 开发工具
*   硬石膏
*   卷曲
*   base64

### 我的第二种方法:

->工具:

*   chrome 开发工具
*   卷曲

->脚本:

*   java 描述语言
*   Python 脚本

**我的 Python 脚本来黑 hackthebox 邀请码:**
[![Alt Text](img/318985eb42d21b56e193743f77de2efd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ss7YWqHY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/udnne9jqzu8216u4oe3n.JPG)
[https://gist . github . com/ankitdobhal/c6ef 926 CD 943383 a5 ada 4a 0 a 8 e 47 FD 8c](https://gist.github.com/ankitdobhal/c6ef926cd943383a5ada4a0a8e47fd8c)

因此，在使用 curl 之后，我找到了代码的加密信息，并使用我的 python 脚本对该信息进行了解码。为了解密代码，我使用了 base64 python 库和 sys 库来传递命令行参数。
- >导入系统
- >导入 base64
[脚本](https://gist.github.com/ankitdobhal/c6ef926cd943383a5ada4a0a8e47fd8c)在我的 github 账户中是可用的。`**谢谢**
所以这是一个挑战在评论区解释你的工具&技巧希望我们能在这个挑战中做出一些惊人的事情。