# 答:如何在几天、几小时、几周和几个月后迭代一个时间跨度？

> 原文：<https://dev.to/ezalivadnyi/answer-how-to-iterate-over-a-timespan-after-days-hours-weeks-and-months-4l6d>

<header>

# ![](img/540c2ba90e5a347bd57c676bb96dfee0.png) [ 回答 re:如何在日、小时、周、月之后的时间跨度上迭代？](https://stackoverflow.com/questions/153584/how-to-iterate-over-a-timespan-after-days-hours-weeks-and-months/155172#155172)

Sep 30 '08[![](img/83f13d376e6608cc602ae93b1cdbae4e.png)111![](img/fd423aaf5fec73c645f97544689ea934.png)](https://stackoverflow.com/questions/153584/how-to-iterate-over-a-timespan-after-days-hours-weeks-and-months/155172#155172) </header>

使用 [dateutil](http://labix.org/python-dateutil) 及其 rrule 实现，如下所示:

```
from dateutil import rrule
from datetime import datetime, timedelta

now = datetime.now()
hundredDaysLater = now + timedelta(days=100)

for dt in rrule.rrule(rrule.MONTHLY, dtstart=now, until=hundredDaysLater):
    print dt 
```

输出是

```
2008-09-30 23:29:54
2008-10-30 23:29:54
2008-11-30 23:29:54
2008-12-30 23:29:54 
```

将每月替换为每年、每月…

<button class="ltag__stackexchange--btn" type="button">[Open Full Answer](https://stackoverflow.com/questions/153584/how-to-iterate-over-a-timespan-after-days-hours-weeks-and-months/155172#155172)</button>