# 面向马来西亚用户的 Kakitangan CLI

> 原文：<https://dev.to/mdminhazulhaque/kakitangan-cli-for-malaysian-users-4ck4>

大家好，我是米娜兹。这是我在 dev.to 的第一篇文章。

在过去的两个月里，我一直在使用命令行版本的 [Kakitangan](https://www.kakitangan.com/platform.html) 这是一款面向马来西亚企业的在线人力资源软件，可以帮助处理工资单、索赔、福利、休假等等。

该项目可在[mdminhazulhaque/kakitangan-CLI](https://github.com/mdminhazulhaque/kakitangan-cli)找到

# 为什么是 CLI 版本？

你可以问我为什么我做这个 CLI 版本，虽然他们有一个很好的网络和移动版本。嗯，像我这样的人更喜欢 CLI 而不是 GUI。另外，我想了解 Python 模块是如何工作的。所以我决定把这个变成一个宠物项目。

# 怎么用？

我目前正在努力通过`setuptools`发布这个。在此之前，您可以下载回购并执行主文件中的`kakitangan.py`。默认命令如下所示。

```
$ kakitangan.py --help
Usage: kakitangan.py [OPTIONS] COMMAND [ARGS]...

Options:
  --help  Show this message and exit.

Commands:
  aboutme      Shows personal information as Kakitangan user
  colleagues   Shows colleagues' leaves
  holidays     Shows holidays
  loadleaves   Fetches all calendar information from Kakitangan server
  login        Login and save authorization token in disk
  logout       Logs out by clearing authorization token from disk
  myleaves     Shows user's leaves
  myleavestat  The user's leave statistics 
```

Enter fullscreen mode Exit fullscreen mode

首先，你得登录，对吗？您必须通过`-u`选项传递用户名/电子邮件。密码将被提示，并且在您键入时不可见。密码也可以通过`-p`选项传递，但是我告诉你不要这样做。你的密码将被存储在`.bash_history`，这将是一个严重的安全问题。

```
$ kakitangan.py login -u john.doe@dream.job
Password: **********
Login success

$ kakitangan.py login -u john.doe@dream.job -p 1234567890
Login success 
```

Enter fullscreen mode Exit fullscreen mode

如果您的密码是正确的，并且您有互联网连接，您应该会看到`Login success`消息。使用`aboutme`选项查看您的基本信息。

```
$ kakitangan.py aboutme
---------------------  -------------------------------
Email                  john.doe@dream.job
Official Full Name     John Doe
Nationality            Bangladesh
Highest Qualification  Bachelors Degree
SOCSO Account          1234567890
Bank Account           1234567890
Bank Account Type      saving
Bank Account Name      AABBCCDD
Income Tax Account     1234567890
Salary                 1234567890
Preferred Name         John
Sex                    Male
Marital Status         married
Active                 True
Birth Date             1994-01-23
Phone                  1234567890
NRIC Number            1234567890
Passport Number        BB12345678
Employee ID            1000
Joined                 1970-01-01
Manager                boss@dream.job
Position               DevOps
Department             Tech
Location               Kuala Lumpur
---------------------  ------------------------------- 
```

Enter fullscreen mode Exit fullscreen mode

我想这对你来说足够了。让我们转到休假日历，这是卡基坦甘最好的功能。在执行任何与日历相关的命令之前，您必须从 Kakitangan 服务器下载完整的日历。`loadleaves`选项会帮你做到。

```
$ kakitangan.py loadleaves
Updated leave database 
```

Enter fullscreen mode Exit fullscreen mode

这样做之后，你会在`~/.config/kakitangan-cli`中发现 3 个新的 JSON 文件，分别命名为`load_calendar.json`、`load_colleagues.json`和`load_holiday_event.json`。这 3 个日历分别保存您自己、您的同事和假期的数据。

现在，您已经准备好获取同事的休假汇总。`colleagues`命令有几个参数。

```
Usage: kakitangan.py colleagues [OPTIONS]

  Shows colleagues' leaves

Options:
  -a, --all          Show past leaves also
  -t, --today        Show leaves only on today
  -u, --untill TEXT  Show leaves upto date
  -s, --since TEXT   Show leaves from date
  -d, --date TEXT    Show leaves on specific date
  --help             Show this message and exit. 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，它会显示当前日期之后的假期。可以看到所有带`-a`参数的叶子。

```
$ kakitangan.py colleagues
---------------  --------------
2019-06-03  Abdul Doe (Annual)
            Alice Begum (Marriage Leave)
2019-06-04  Abdul Doe (CarryForward)
            Charlie Ahmed (Annual)
            Foo Bin Bar (Annual)
            Alice Begum (Sick)
2019-06-10  Foo Bin Bar (Sick)
            Bob Mia (Birthday Leave)
2019-06-11  Bob Mia (Sick)
2019-06-12  Bob Mia (Sick)
2019-06-13  Bob Mia (Annual)
2019-06-14  Clara Khatun (Annual)
            Bob Mia (Annual)
2019-06-15  Clara Khatun (CarryForward)
2019-06-16  Clara Khatun (CarryForward)
2019-06-17  Clara Khatun (Annual)
            Bob Mia (Replacement)
2019-06-18  Bob Mia (Annual)
---------------  -------------- 
```

Enter fullscreen mode Exit fullscreen mode

可以看到谁在特定日期休假。只需用`-d`参数传递`YYYY-MM-DD`格式的日期。

```
$ kakitangan.py colleagues -d 2019-06-10
---------------  -----------
2019-06-10  Foo Bin Bar (Sick)
            Bob Mia (Birthday Leave)
---------------  ----------- 
```

Enter fullscreen mode Exit fullscreen mode

您可能希望筛选特定日期范围内的同事。只需使用`-s`(自)和`-u`(直到)参数即可。

```
$ kakitangan.py colleagues -s 2019-06-15 -u 2019-06-17
---------------  ------------
2019-06-15  Clara Khatun (Annual)
2019-06-16  Clara Khatun (Annual)
2019-06-17  Clara Khatun (Annual)
            Bob Mia (Annual)
---------------  ------------ 
```

Enter fullscreen mode Exit fullscreen mode

类似于休假日历，你也可以查看你的假期。为此，使用`holidays`选项。

```
$ kakitangan.py holidays 
---------------  ----------------------------
2019-06-05  Hari Raya Aidilfitri
2019-06-06  Hari Raya Aidilfitri Holiday
2019-08-11  Hari Raya Haji
2019-08-12  Hari Raya Haji
2019-08-31  Merdeka Day
2019-09-01  Awal Muharram
2019-09-02  Awal Muharram
2019-09-09  Agong's Birthday
2019-09-16  Malaysia Day
2019-10-27  Deepavali
2019-10-28  Deepavali
2019-11-09  Prophet Muhammad's birthday
2019-12-25  Christmas
---------------  ---------------------------- 
```

Enter fullscreen mode Exit fullscreen mode

正如我前面所说，过滤隐藏了旧的叶子/事件。所以你必须通过`-a`才能看到完整的假期列表。

```
$ kakitangan.py holidays -a
---------------  ---------------------------------------------------------
2015-01-01  New Year
2015-01-02  New Year
2015-01-03  New Year
...
2017-06-25  Hari Raya Aidilfitri
2017-06-26  Hari Raya Aidilfitri
2017-06-27  Hari Raya Aidilfitri
...
2019-10-27  Deepavali
2019-10-28  Deepavali
2019-11-09  Prophet Muhammad's birthday
2019-12-25  Christmas
---------------  --------------------------------------------------------- 
```

Enter fullscreen mode Exit fullscreen mode

好吧，你想检查你自己的叶子，对吧？你可以！如果您也想看到老叶子，请使用带`-a`参数的`myleaves`。

```
$ kakitangan.py myleaves -a
---------------  -----------
2019-02-28  Sick
2019-03-12  Sick
2019-03-21  Annual
2019-05-17  Annual (PM)
2019-05-21  Annual
2019-05-22  Annual
2019-05-23  Annual
2019-05-24  Annual
---------------  ----------- 
```

Enter fullscreen mode Exit fullscreen mode

如果你需要知道你的休假统计，我添加了一个名为`myleavestat`的命令。

```
$ kakitangan.py myleavestat
----------------------------  -----------------
Compassionate            10/10
Annual                   25/50 (25 used)
Sick                     15/30 (15 used)
Paternity                10/10
Hospitalization          30/60 (30 used)
Replacement              1/1
Birthday Leave           1/1
Annual Leave Additional  5/5
CarryForward             5/5
Unpaid Leave             10/10
Bonus Leave              1/1
Exam/Study Leave         5/5
----------------------------  ----------------- 
```

Enter fullscreen mode Exit fullscreen mode

回家吗？不想让任何人偷偷进入你的 Kakitangan 账户？你可以随时注销。很简单。

```
$ kakitangan.py logout
Confirm logout? [Y/N]: Y
Logged out 
```

Enter fullscreen mode Exit fullscreen mode

请检查、发布问题、启动或提交更改。谢了。