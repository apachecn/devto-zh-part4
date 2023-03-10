# 用 Python 实现 HN 的网络抓取

> 原文：<https://dev.to/petercour/web-scraping-hn-with-python-41op>

你可能知道 HN。一个科技文章的新闻聚合器。

让我们用 Python 刮一下。PyQuery 模块允许您查询 HTML 页面。您可以使用 PyQuery 收集所有链接

```
#!/usr/bin/python3
from pyquery import PyQuery as pq

doc =pq(url = "https://news.ycombinator.com/front?day=2019-07-14" )

for link in doc('a.storylink'):
    print(link.attrib['href']) 
```

这将返回日期为“2019-07-14”的链接。这样，您就有了一个打印到屏幕上的链接列表。你想把它写进文件里。

[![](img/b29332242d47b9ce976bd2822b368234.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ekAK8rPx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.tenor.co%252Fimages%252Fb910eef482ebf7f15aa6da0cc648f827%252Fraw%26f%3D1)

曲棍球码头。

您可以将输出保存到 csv 文件中。csv 文件是一个包含所有存储值的文件，中间有一个分隔符，通常是冒号，但我们将使用分号。

```
#!/usr/bin/python3
from pyquery import PyQuery as pq

date = "2019-07-14"
doc =pq(url = "https://news.ycombinator.com/front?day=" + date )

links = []
for link in doc('a.storylink'):
    links.append(link.attrib['href'])

with open('output.csv','w+') as csvfile:
    for link in links:
        csvfile.write( date + ";" + link + ";" )
        csvfile.write('\n') 
```

简单吧？:)运行它，你将得到一个格式良好的 csv 文件中的所有链接。

csv 文件可以用 office 程序(任何电子表格)读取，或者您可以使用 [Python pandas](https://pythonprogramminglanguage.com/read-csv/) 来读取它们。

相关链接:

*   [学习 Python 编程](https://pythonbasics.org/)
*   [Python 练习](https://pythonbasics.org/exercises/)