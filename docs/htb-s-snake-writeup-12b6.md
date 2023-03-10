# 黑盒子里的蛇-我的评论

> 原文：<https://dev.to/edoardopigaiani/htb-s-snake-writeup-12b6>

## 免责声明

如果你对剧透感到不舒服，现在就停止阅读。

## 挑战

在等待 [SwagShop](https://www.hackthebox.eu/home/machines/profile/188) 撤下以发表我的文章时，我抓住机会解决了 HackTheBox 上的几个挑战，从 Snake 开始。

贪吃蛇是 3XPL017 的反转挑战，你可以在这里找到它[。](https://www.hackthebox.eu/home/challenges/Reversing)

下载并使用提供的密码解压存档文件后，我们会看到一个名为 *snake.py* 的 Python 脚本，我们将尝试运行它:

[![Script running](img/a85f2a65fa7fbbe8dd358c621aa68ae0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_eeVLlw8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/07fOOYr.png)

在分配给我们一个随机数之后，is 会要求我们输入用户名和密码，我们将通过查看代码来深入研究这个问题。

[![Script code](img/c62403143c563b49849e5c962cedd4bd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h4IMnN97--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/rCzLtEc.png)

我们可以注意到声明了很多变量，其中最突出的是 *slither* ,因为它似乎是在提示输入用户名时正确识别所需的变量。

```
if user_input == slither:
    pass 
```

因此我们可以加上:

```
print slither 
```

为了打印变量 slither，这是需要的用户名。脚本部分将如下所示:

[![Script code](img/ec29acdaef3a649838fd6fd674df675c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H6u8l2sA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/NqVSRMQ.png)

一旦我们运行它，我们会得到:

[![Script code](img/3f3ffbda04c99ba98b2efe91a67d9589.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NvKLMA_y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/Jm9ktZt.png)

是的， *anaconda* 有道理，确实是需要的用户名。

让我们来看看密码，它是由脚本的这一部分生成的:

[![Script code](img/9211997ef7477bb0b8c43eb3c0506b21.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FYYMmr5U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/PVqFSSa.png)

我们可以看到*通过*与 *(chr(char))* 进行比较

功能:

*   *str()*–返回一个字符串
*   *chr()*–返回一个字符，之后接受一个 unicode 数字的参数

变量:

*   *char*–数组字符中的一个元素
*   *chars*–初始化为空数组

数组字符包含:

```
chars = [] 
```

还有:

```
for key in keys:
    keys_encrypt = lock ^ key
    chars.append(keys_encrypt)
for chain in chains:
    chains_encrypt = chain + 0xA
    chars.append(chains_encrypt) 
```

还有一个*中断*，它终止循环，即使单个字符匹配用户给定的输入。

解决这个问题的最简单的方法是在要求输入密码之前打印字符，我们可以通过修改脚本来做到这一点:

[![Script code](img/444cb332141dcd21c6ca3322e04b93f0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6n_HxaSE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/PhNT3CP.png)

如果我们运行它，我们会得到:

[![Script code](img/5ec6bb3b71734007342925a1e049a216.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DdZf2NPg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/cZLmLM2.png)

密码是有效的，由于*键*包含密码的前 10 个字符，我们可以假设我们需要输入它们以正确解决挑战。

旗帜格式是 *HTB{user:password}* ，所以正确的输入方式是 *HTB{anaconda:udvvrjwa$$}* 。

解决了！