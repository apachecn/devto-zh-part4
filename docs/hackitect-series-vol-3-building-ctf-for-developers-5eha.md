# Hackitect 系列第 3 卷——为开发人员构建捕获旗帜

> 原文：<https://dev.to/sottlmarek/hackitect-series-vol-3-building-ctf-for-developers-5eha>

## 开始游戏

我一直喜欢游戏。在我的上一个项目中，我拼命地为开发者寻找一些游戏化的东西。我正在提供关于 SSDLC 的培训，这总是很难教授不同的漏洞及其影响。把架构师和开发者联系在一起并不容易。最好的方法就是弄脏我们的手。解决方案出乎意料地出现了。OWASP Juice Shop 和脸书 capture the flag 提供了预定义漏洞和代码攻击可扩展性的强大组合。

因此，让我们开始我们的旅程，建立有趣的黑客游戏化捕捉旗帜平台。

## 工作原理

为了学习 OWASP TOP 10，我们将与 Capture the flag portal 和名为 Juice Shop 的易受攻击的应用程序一起构建平台。易受攻击的电子商店包含大约 90 种不同的挑战，难度不断增加。每一个被利用的漏洞挑战都会得到成就的回报。与 CTF 的集成是通过标志哈希完成的。另一方面，有一个基于脸书开源的世界统治 CTF 平台。各队根据测验征服各个国家，并通过寻找漏洞来捕捉旗帜。您将旗帜散列复制粘贴到 CTF 平台，并捕获包含挑战的选定国家。被占领的国家给你分数。分数的多少取决于挑战的难度。
在给定的时间内，得分最高的队伍赢得比赛。快乐的威胁狩猎和不要忘记主要价格-巧克力。

## 第一步

安装 OWASP 果汁店。可以在 windows 上安装 juice shop)非常容易)或者 ubuntu 作为真正极客的推荐选项。Node.js for Windows 的安装非常容易，所以我看不出有任何解释的理由。

**对于 Windows，需要打开命令行并:**

1.  安装 git bash
2.  `git clone https://github.com/bkimminich/juice-shop.git` >在我们的工作目录中
3.  打开果汁店目录
4.  `npm install`
5.  `npm start`
6.  打开浏览器，点击本地主机:3000

**对于 Linux 你需要打开命令行并且(在 Ubuntu 18.04.2 LTS 上测试过):**

1.  下载打包果汁店([https://github . com/bkim minich/Juice-shop/releases/tag/v 8 . 7 . 2](https://github.com/bkimminich/juice-shop/releases/tag/v8.7.2))
2.  解压缩并放入解压缩的文件夹
3.  或者(不推荐效果不好):`sudo apt install curl` `curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -` `sudo apt install node.js`
4.  打开`juice-shop`目录
5.  `npm install`(备选)/运行`npm start`打包。
6.  打开浏览器，点击本地主机:3000

现在我们完成了果汁店准备推出它包含不同水平的新手和铁杆非睡眠者史诗般的挑战。

*会是这样:*
[![](img/b7cc1b3e1813656b0f2838d0304fafb4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eYmJ5nUI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wdacwfbbdflom7acsup6.png)
[![](img/d01f384878e7e951664b55fb47790189.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zvKhW3F8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l76o8ivefsk72fvw0tcv.png)

游戏已经准备好了，但是还没有完全准备好，我们需要把我们的 CTF 旗帜偷偷带进平台，然后把它们导入 FBCTF。玩游戏好玩吗？别担心，好戏马上就要上演了。

## 第二步-设置环境

您需要设置环境变量。安装后，请重新启动您的机器，并设置根或管理员访问下的环境变量。

1.  在 Linux 的 Windows `export NODE_ENV=ctf`中设置 NODE_ENV 变量`set NODE_ENV=ctf`
2.  重启你的机器
3.  打开 cmd 或端子`npm start`

## 第三步-为 FBCTF 生成数据

通过这里提到的向导生成数据:[来自作者](https://github.com/bkimminich/juice-shop-ctf)的向导

**我推荐以下设置:**

```
ctfFramework: FBCTF
juiceShopUrl: https://juice-shop.herokuapp.com
ctfKey: https://raw.githubusercontent.com/bkimminich/juice-shop/master/ctf.key 
countryMapping: https://raw.githubusercontent.com/bkimminich/juice-shop/master/config/fbctf.yml 
insertHints: none
insertHintUrls: none 
```

## 第四步-设置&进口信息 FBCTF

我安装失败了很多次。如果你不确定要查看 Github 上的官方指南。请注意，admin 的密码会在安装过程中出现在命令行中，请注意检查 cmd 输出。CTF 框架需要安装在 Ubuntu 16.04 上。

**安装 FBCTF:**

```
sudo apt-get update
sudo apt-get install git
git clone https://github.com/facebook/fbctf
cd fbctf
./extra/provision.sh -m prod -s $PWD
source ./extra/lib.sh
set_password [new_password] ctf ctf fbctf $PWD 
```

**导入带有数据的 JSON:**

1.  转到本地主机
2.  登录管理员
3.  转到控制，并选择进口完整的游戏。
4.  选择*。juice-shop-ctf 生成的 json 数据文件
5.  看起来太棒了！
6.  更多信息请查看[管理指南](https://github.com/facebook/fbctf/wiki/Admin-Guide)

[![](img/b830693762eea72bb62926f4dc9d49e8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LzwiWmEh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lah7b5m2ot26nioizgqb.png)
[![](img/bc858eb0152e51fc7b5e80402b704dc5.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--k_Wzhnsk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/899gjv6x4j6a4i1aa155.png)

不要忘记创建团队，设置计时器并选择身份验证方法。请随意定制您的徽标。

感谢开源软件的作者，我在安装过程中遇到了很多困难。我希望我们能在其中一个会议上见面，一起培训如何黑掉一些易受攻击的应用程序。享受你的黑客生涯。

如果你有兴趣在我的培训课上与我见面，你可以在 ICTpro 学校[这里](https://www.skoleni-ict.cz/kurz/Bezpecny-vyvoj-aplikaci-SSDLC-HCK4.aspx)报名参加培训