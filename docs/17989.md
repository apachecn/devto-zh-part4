# 面向初学者的 Python 编程资源集

> 原文：<https://dev.to/prahladyeri/a-collection-of-python-programming-resources-for-the-beginners-1i8k>

*(最初发表于[https://github.com/prahladyeri/CuratedLists/](https://github.com/prahladyeri/CuratedLists/blob/master/lists/python.md))*

# Python 编程资源

## 公文等资源

*   [Python 官方文档](https://docs.python.org/)
*   [PEP-8 -编写 python 代码的指南](https://www.python.org/dev/peps/pep-0008)
*   [Python 包索引——官方 Python 包回购](https://pypi.python.org/)
*   [/r/learnpython wiki](https://www.reddit.com/r/learnpython/wiki/index#wiki_practice_python)
*   用于 Python 扩展包的非官方 Windows 二进制文件 -必须为 Python 的 Windows 用户提供资源。在 pip installable(*)中提供所有已编译的包文件。文件。
*   [Python 海信](https://python.zeef.com/alan.richmond)

## 库

### 数据库连接

*   [mysql-connector-python](https://dev.tomysql-connector-python)-Oracle 官方 MySQL 客户端库。
*   PyMySQL -一个纯 Python 的 MySQL 客户端库，基于 PEP 249。[显然比官方司机](https://stackoverflow.com/a/25724855/849365)快
*   另一个 mysql 客户端库的实现。[显然，最快的实现是基于 C 的](https://stackoverflow.com/a/25724855/849365)。Django 的推荐，也被 debian 和 ubuntu repos 用于`python3-mysqldb`包。
*   最流行的 python PostgreSQL 驱动程序。
*   [SQLAlchemy](http://www.sqlalchemy.org/)——python 世界中的“面包和黄油” [ORM](https://en.wikipedia.org/wiki/Object-relational_mapping) 库。极其通用和灵活，足以与人类已知的几乎任何 RDBMS 一起工作。

### 科学/统计

*   对数学家和统计学家来说很重要的 python 包。
*   用于科学计算的软件包。
*   一个 2d 绘图库。
*   [pandas](http://pandas.pydata.org/)-Python 的高性能数据分析库。
*   [nltk](http://www.nltk.org/)——在*自然语言处理领域广泛使用的 Python 库*。

### 机器学习

*   [gpt-2](https://github.com/openai/gpt-2) - Neuralnet/AI 库，代码来自论文《语言模型是无监督的多任务学习器》。
*   scikit-learn-Python 编程语言的免费软件机器学习库。
*   tensorflow -一个免费的开源软件库，用于数据流和一系列任务的差异化编程。

### 联网/刮

*   [requests](http://docs.python-requests.org/en/latest/) -一个用于处理 HTTP 请求的流行库。
*   [Twisted](https://twistedmatrix.com/)——用 python 编写的事件驱动的网络引擎。Twisted 对于 python 就像 System.Net 对于 C#或者 java.net 包对于 Java 一样。
*   机械化——用 Python 编写的成熟的网络抓取框架。

### 电子表格/文档

*   [openpyxl](http://openpyxl.readthedocs.org)——一个纯 python 库，以`Microsoft Excel`格式导出/导入数据。
*   [xlrd](https://blogs.harvard.edu/rprasad/2014/06/16/reading-excel-with-python-xlrd/) -读取微软 Excel 97 (*)的纯 python 库。xls)格式工作簿。
*   [python-docx](https://python-docx.readthedocs.io) - Python 库来编写与 Microsoft Word 文档一起工作。
*   [markdown](https://pypi.org/project/Markdown/) -使用 markdown 格式的 Python 库。
*   Pandoc -将文档从一种格式转换成另一种格式的绝妙工具，支持 markdown、reST、doc、docx、pdf 和许多其他格式。
*   这是一个令人兴奋的 XML 解析库，广泛用于 python 中的 web 抓取脚本和应用程序。
*   [lxml](https://lxml.de) -用 python 处理 xml/html 的库。

### 构建和开发

*   [Twine](https://github.com/pypa/twine/) -用于与 PyPi 包装系统交互的实用程序。
*   PyTest——可能是 python 世界中事实上的测试模块。
*   sphinx-python 的标准文档工具。

### 其他

*   一个支持 markdown 和 reST 语法的静态站点生成器。用 python 写的。
*   一个用 Python 写的语法高亮包。
*   pygame -一个流行的 python 游戏库。
*   [panda3d](https://www.panda3d.org/) - Python 图形库。
*   [pyglet](http://www.pyglet.org/) - Python 图形库。
*   Kivy -一个用于构建应用程序的现代 GUI 工具包，可以在 Linux、Android、Windows 和 Mac 上无缝运行。
*   Python 中的另一个 GUI 工具包，PyQt 的开源版本，是商业化的。
*   另一个类似于 PyQT 和 tkinter 的 GUI 工具包。
*   使用 twitter api 访问 Tweepy 的库。
*   pytz -处理时区的库。
*   [tqdm](https://github.com/tqdm/tqdm) -立即让你的循环显示智能进度表。

## 网络框架

*   Django -用 Python 编写的流行的 web 框架。Django 经常被吹捧为有截止日期的忙碌记者的 web 框架，它非常灵活，可以适应任何 web 项目的需要。
*   烧瓶 -一个极简的网络框架。虽然不像 Django 那样流行和充实，但 Flask 遵循了一种更加`lego block`的开发方法——从最小的核心开始，并根据需要不断增加。然而，这是一个优秀的框架。
*   [Bottle](http://bottlepy.org/)——Python 的单模块 web 框架。

## 在线教程、书籍、拼图

*   [深入 Python - Mark Pilgrim](https://linux.die.net/diveintopython/html/toc/index.html) -所有 Python 学习者和爱好者的必读之作。
*   [使用 Python 的网页抓取 101](http://www.gregreda.com/2013/03/03/web-scraping-101-with-python/)
*   [用 Python 写虚拟机](http://pythonguy.wordpress.com/2008/04/17/writing-a-virtual-machine-in-python/)
*   [编码 Bat - Python](http://codingbat.com/python)
*   [简单 Python 程序](https://wiki.python.org/moin/SimplePrograms)
*   [Python 初学者指南](https://wiki.python.org/moin/BeginnersGuide)
*   [学习 Python，破解 Python -初学者指南](http://learnpythonbreakpython.com/)
*   [用 Python 自动化枯燥的东西](http://automatetheboringstuff.com/)

## 热门 Youtube 频道

*   科里·斯查费
*   工程师
*   [真正的蟒蛇](https://www.youtube.com/channel/UCI0vQvr9aFn27yR6Ej6n5UA)