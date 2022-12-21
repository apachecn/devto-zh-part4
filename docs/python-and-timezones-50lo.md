# Python 和时区

> 原文：<https://dev.to/tonetheman/python-and-timezones-50lo>

我最近需要知道另一个时区的时间。同样，这对 Python 来说是一个好任务(一切都是真的)。

有一个名为钟摆的很棒的包就是为此而制作的。

看这里:[https://pendulum.eustace.io/](https://pendulum.eustace.io/)

所以如果我需要知道日本现在是几点...

```
import pendulum
now_in_japan = pendulum.now("Japan")
now_in_japan 
```

Enter fullscreen mode Exit fullscreen mode

这是很容易阅读，并显示我正是我想要的。

另外，如果您想知道您的系统了解哪些时区，请在/usr/share/zoneinfo 中查找。不确定 windows:(