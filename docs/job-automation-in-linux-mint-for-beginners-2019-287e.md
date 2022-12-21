# 面向初学者的 Linux Mint 2019 中的作业自动化

> 原文：<https://dev.to/softhints/job-automation-in-linux-mint-for-beginners-2019-287e>

自动化有助于枯燥乏味的工作，节省时间和精力(当然，如果你做得对)。Linux 中的自动化和任务调度是通过名为 crontab(简称 CRON)的守护进程完成的。以下定义最恰当地描述了该工具:

> cron 是一个 Unix 实用程序，它允许任务由 cron 守护进程定期在后台自动运行。

在本帖中，您可以找到几个 cron 和 bash 脚本的例子，以便自动化一些任务，如:

*   面向初学者的 Crontab
*   [从你的 Linux 机器上随机播放歌曲](#music)
*   [在指定时间开始激励/放松视频](#video)
*   [在 Linux Mint 启动时打开随机文本文件](#text)
*   [抓取 web 数据并加载 LibreOffice(或 csv、excel 等)](#scraping)
*   [将简单报表生成为 doc 文件，创建 jupyter 记事本并发送邮件](#report)

所有的例子都是在 Linux Mint 19 Cinnamon 上编写和测试的。我计划为上面的一些主题准备额外的文章和视频。

更多关于自动化的文章:

*   [在 Windows 和 Linux 中自动执行任务](https://blog.softhints.com/automate-tasks-in-windows-and-linux/)
*   [在 Linux Mint 19 / Ubuntu 18.04 中从终端启动和停止应用](https://blog.softhints.com/start-stop-application-terminal-linux-mint/)

## Crontab 适合初学者

为了给你的用户安排作业，你需要写:

```
crontab -e 
```

该命令的描述是:

> 编辑 crontab 文件，如果它不存在，则创建一个

现在您可以通过:
列出 Linux Mint / Ubuntu 中的计划作业

```
crontab -l 
```

该命令将:

> crontab cron jobs 列表，显示 crontab 文件内容。

cron 作业示例:

```
0 16 * * * DISPLAY=:0 thunderbird %u 
```

这将在 16:00 打开雷鸟邮件客户端。其中:

| 分钟 | 小时 | 天 | 月 | 日(周) | 命令 |
| --- | --- | --- | --- | --- | --- |
| Zero | Sixteen | * | * | * | DISPLAY=:0 雷鸟%u |
| Fifty-five | 9-15 | * | * | * | DISPLAY=:0 ~/motivation.sh |

## 从你的机器上开始随机点歌

为了提高工作效率和保持健康，你需要定期休息。在电脑上工作使这个习惯变得困难，你很容易忘记时间。为了有规律的休息和获得一些动力，一个简单的脚本可以用来每小时播放音乐。

下面你可以找到一个简单的脚本，它从一个文件夹中随机选择一首歌，然后用这首歌启动播放器，最后关闭播放器。

```
#!/bin/bash
MUSIC=/mnt/x/Music/Motivation/
cd $MUSIC
ls |sort -R |tail -1 |while read file; do echo "$MUSIC$file"
    rhythmbox "$MUSIC$file" & sleep 5m
done killall rhythmbox 
```

该脚本的简短说明:

*   这是一个约定，所以 Unix shell 知道运行哪种解释器
*   下一个是包含歌曲的文件夹，从中可以随机选择一首。用文件夹定义一个变量，并将当前文件夹更改为该变量
*   `ls |sort -R |tail -1 |while read file; do` -选择一首随机歌曲，并将其分配给变量文件，该文件将在下一条语句中使用
*   `rhythmbox "$MUSIC$file" & sleep 5m` -启动播放所选歌曲的音乐播放器，并保留脚本 5 分钟。此文件夹中歌曲的最大长度
*   `killall rhythmbox` -终于干掉了音乐播放器

一旦您有了脚本(例如将它命名为 **motivation.sh** ，将它放在您的家中并允许它执行)，您就可以通过以下方式轻松地调度新的 cron 作业:

*   用`CTRL` + `ALT` + `T`打开新的终端
*   类型:

```
crontab -e 
```

*   在文件末尾添加:

```
0 9-17 * * * DISPLAY=:0 /home/user/cron/motivation.sh 
```

如果您希望歌曲在 9 点到 17 点之间每小时播放一次。快乐聆听！

**以科学为后盾:**

> 2005 年,《音乐心理学杂志》的研究表明，软件开发人员在听音乐时会体验到更积极的情绪、更好的工作质量和更高的效率。

[科学支持音乐影响大脑和生产力的方式](https://medium.com/the-mission/the-science-backed-ways-music-affects-your-brain-and-productivity-e11145079305)

[![Guitar](img/02ab54bddb2d752cc4036eafbb629a07.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---dN1H_bB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/os7uf1q9w2gjv0799qow.jpg)

## 在指定时间开始激励/放松视频

下一个例子将演示如何用几行代码和 crontab 守护进程让您的工作暂停放松。这个例子帮助我安排一个冥想视频，每天几次，每次 10 分钟放松。基本上你可以使用任何你喜欢的视频或歌曲，这完全取决于你的个人喜好。

通常我会使用朋友分享的冥想音乐或瑜伽视频。如果你没有什么好的想法，那么你可以从 Youtube 上下载任何视频并使用它(只需搜索放松的音乐或放松)。

剧本是:

```
#!/bin/bash
vlc -f /home/user/Videos/Relaxing.mp4 & sleep 10m
killall vlc 
```

*   将脚本保存为文件夹`/home/user/cron/motivation.sh`中的**motivation . sh**——或者您喜欢的名称
*   启动 linux 终端
*   类型:

```
crontab -e 
```

*   在文件末尾添加:

```
0 10 * * * DISPLAY=:0 /home/user/cron/motivation.sh 
```

这将启动 VLC 播放器与选定的视频在上午 10 点。会让它持续运行 10 分钟，最后杀死玩家。所以我每个工作日 10 点休息。

#### 如何在 Ubuntu / Linux Mint 下载 youtube 视频:

*   通过以下方式安装 youtube-dl

```
sudo apt-get install youtube-dl 
```

*   通过以下方式下载视频:

```
youtube-dl https://www.youtube.com/watch?v=mkKDI6y2kyE 
```

额外收获:对于家庭作业，你可以尝试找出如何下载多个视频并更新脚本，以便随机播放一个。

[![Meditation](img/dec0898e76bfe7176d8658705059103d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zvOp4Y16--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cvt58hwffr1ogyq3dait.jpg)

**以科学为后盾:**

*   定期休息有助于:
    *   提高生产率
    *   改善精神健康
    *   创造力提升
    *   更多时间培养健康习惯

来源:[新研究显示员工敬业度和久违的午休时间之间的相关性](https://www.forbes.com/sites/alankohll/2018/05/29/new-study-shows-correlation-between-employee-engagement-and-the-long-lost-lunch-break/#3cd5c88d4efc)

## 在 Linux Mint 启动时打开随机文本文件

这个例子演示了如何在计算机启动时自动执行任务。对我来说，喜欢一篇文章或一句话很容易，但忘记它就更容易了。所以我有一个脚本，它在机器启动时随机加载一段文本。用这种方法，我可以从一本书、一篇文章或我的任务清单中保留一些好的读物:)

下面你可以找到自动化的脚本:

```
cd /home/user/Documents/Books/Motivation
ls |sort -R |tail -1 |while read file; do xed $file
done

: '
This script open random text file
' 
```

这个脚本展示了与第一个例子类似的技术。这里面有两个新东西:

*   `xed` -使用 xed 文本编辑器打开文本文件
*   演示如何使用 bash 注释来为脚本获取有意义的信息

最后，为了安排启动机器，您可以:

*   末端的
*   类型:

`crontab -e`

然后:

`@reboot /home/user/cron/openRandomText.sh`

或者您可以使用系统启动应用程序，如(对于 Linux Mint):

*   炙单
*   偏好；喜好；优先；参数选择
*   启动应用程序
*   添加(加号按钮)
*   自定义命令和类型:
    *   名字
    *   命令
    *   启动延迟

现在，在开始一天的工作(或其他任何一天)之前，你将拥有所有重要的笔记。

**以科学为后盾:**

> 我们之所以记得事情，是因为它们要么很突出，它们与我们现有的知识库相关，并且可以很容易地整合到我们现有的知识库中，要么是我们随着时间的推移反复检索、叙述或使用的东西

来源:[如何提高记忆力，根据神经科学](https://www.nbcnews.com/better/health/how-get-better-remembering-things-according-neuroscience-ncna882426)

## 抓取 web 数据，加载 LibreOffice(或 csv、excel 等)

如今，网络数据非常重要和珍贵。收集是通过特殊的程序(称为蜘蛛或刮刀)以快速、可靠和自动化的方式完成的。通过使用像 scrapy 这样的模块，编写一个个人蜘蛛是非常容易的。当我需要买东西时，我会使用蜘蛛，并定期查看目标产品的价格——这样我就能得到很好的推广。

下面你可以找到非常简单的蜘蛛抓取网站和收集数据:

```
import scrapy

class QuotesSpider(scrapy.Spider):
    name = "quotes"

    def start_requests(self):
        urls = [
            'http://quotes.toscrape.com/page/1/',
            'http://quotes.toscrape.com/page/2/',
        ]
        for url in urls:
            yield scrapy.Request(url=url, callback=self.parse)

    def parse(self, response):
        page = response.url.split("/")[-2]
        filename = 'quotes-%s.html' % page
        with open(filename, 'wb') as f:
            f.write(response.body)
        self.log('Saved file %s' % filename) 
```

你可以在: [Scrapy 教程](https://docs.scrapy.org/en/latest/intro/tutorial.html)找到更多信息

一旦你的蜘蛛完成，你可以创建脚本来自动执行。下面你可以找到一个示例脚本:

```
#!/bin/bash
cd /home/user/environments/venv36
source ./bin/activate
cd /home/user/PycharmProjects/python/src/spiders/

now=$(date +"_%Y-%m-%d")

scrapy runspider my_spider.py -o - >data/my_spider/my_spider$now.csv -t csv
python helpers/pandas_after.py

libreoffice --calc /home/user/data/my_spider$now.csv 
```

现在让我们来谈谈上面的代码:

*   前两行用于为这个蜘蛛激活特殊的 python 环境
*   然后将工作文件夹更改为 spider 项目
*   下面一行运行蜘蛛程序，并将输出保存为 csv 文件
    *   `scrapy runspider my_spider.py -o - >data/my_spider/my_spider$now.csv -t csv`
*   命令`python helpers/pandas_after.py` -调用 pandas 函数来清理、添加新列和排序数据
*   最后一行在 Libre Office 中打开新创建的 CSV 文件

这个脚本在我的机器上用下面的 crontab 调度:

```
55 9 * * * DISPLAY=:0 /home/user/cron/spider_script.sh 
```

**P.S.** 如果您删除了(顺便用 crontab -r)您的 crontab 作业，可以通过检查日志来恢复(如果它们正在运行):

```
grep 'CRON.*username' /var/log/syslog 
```

其中用户名是您的用户名。

[![Adult](img/dec0898e76bfe7176d8658705059103d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zvOp4Y16--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cvt58hwffr1ogyq3dait.jpg)

## 将简单报表生成为 doc 文件，创建笔记本并发送邮件

最后一个例子展示了一个小 python 程序，它从 Github 等各种来源收集数据。下面的程序将收集关于给定帐户的 Github 信息:

```
class Github:
    def getStars(self):
        for repo in g.get_user('Softhints').get_repos():
            print(repo)
            stars = repo.stargazers_count
            forks = repo.forks_count
            print(stars)
            print(forks)
            print(repo.get_topics())

            open_issues = repo.get_issues(state='open')
            for issue in open_issues:
                print(issue)
            return stars, forks 
```

我有类似的不同 API 的小类和网站，如 Youtube，脸书，twitter 等。他们每周收集我感兴趣的信息。

另一个真正有用的工具是 Selenium，它可以帮助实现 web 自动化。下面你可以找到谷歌搜索的基本自动化:

```
from selenium import webdriver
from selenium.webdriver.chrome.options import Options

# options.add_argument('--headless') 
executable_path = r'/home/userchromedriver_linux64/chromedriver'

options = Options()
driver = webdriver.Chrome(    executable_path=executable_path,    chrome_options=options)

driver.get("https://google.com")
search = driver.find_element_by_name('q')
search.send_keys("softhints python automation")

print(driver.page_source.encode("utf-8"))
driver.quit() 
```

对于此代码，您需要:

*   从[chrome driver-web driver for Chrome](https://chromedriver.chromium.org/downloads)获取最新的 Chrome 驱动程序
*   用最新路径更新脚本
*   在网页上查找元素
    *   在这一步你可以使用真正好的浏览器扩展- [Katalon -简化 API，Web，移动自动化测试](https://www.katalon.com/)
*   发送搜索信息
*   获取页面源

最终的数据收集成 csv 文件存储在电脑上，加载到 Jupyter 笔记本上，最后小报告发给我。如果我觉得这个报告有趣，我可以在笔记本上找到更多的细节。

该脚本的示例代码如下所示:

```
#!/bin/bash
cd /home/user/environments/venv36
source ./bin/activate
cd /home/user/PycharmProjects/automation/src/

python weekly.py
python create_report.py
python create_notebook.py
python send mail.py 
```

示例代码的不同部分可以在下面找到:

#### Python 如何创建 doc 文件

```
from docx import Document
from docx.shared import Inches

document = Document()

document.add_heading('Document Title', 0)

p = document.add_paragraph('A plain paragraph having some ')
p.add_run('bold').bold = True

document.add_heading('Heading, level 1', level=1)
document.add_paragraph('Intense quote', style='Intense Quote')

document.add_paragraph(
    'first item in unordered list', style='List Bullet'
)
document.add_paragraph(
    'first item in ordered list', style='List Number'
)

document.save('demo.docx') 
```

更多信息请点击此处:

用于创建和更新 Microsoft Word 的 Python 库。docx)文件。

#### Python 如何创建 jupyter 笔记本

```
import nbformat as nbf

nb = nbf.v4.new_notebook()
text = """\ # Weekly report
Report for github, youtube, etc."""

code = """\ # Import pandas 
import pandas as pd 

# reading csv file  
pd.read_csv("report.csv") """

nb['cells'] = [nbf.v4.new_markdown_cell(text),
               nbf.v4.new_code_cell(code)]
fname = 'report.ipynb'

with open(fname, 'w') as f:
    nbf.write(nb, f) 
```

笔记本可以用几种不同的方式创建。我有两种方法第一种(上面的一种使用 nbformat)第二种使用 json 模板并使用 ipython API 创建笔记本。

#### Python 如何发送邮件

```
# Import smtplib for the actual sending function import smtplib

# Import the email modules we'll need from email.message import EmailMessage

# Open the plain text file whose name is in textfile for reading. with open(textfile) as fp:
    # Create a text/plain message
    msg = EmailMessage()
    msg.set_content(fp.read())

# me == the sender's email address
# you == the recipient's email address msg['Subject'] = 'The contents of %s' % textfile
msg['From'] = me
msg['To'] = you

# Send the message via our own SMTP server. s = smtplib.SMTP('localhost')
s.send_message(msg)
s.quit() 
```

更多信息请点击: [Python 电子邮件:示例](https://docs.python.org/3/library/email.examples.html)

**结论:**自动化可以是你最好的朋友，也可以是你的噩梦——一切都取决于你。一般来说，自动化可能会增加依赖性和复杂性，但它可以节省时间和精力。

我的个人目标是自动化任何可以自动化的东西，并且它会在时间上重复。今天投资一天，未来节省一个月。这条规则帮助我用更少的时间做更多的工作。当然，在给定的任务上有迟到的风险，但以后这种投资会得到回报。