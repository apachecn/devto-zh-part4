# 分心，这到底是谁的生活？

> 原文：<https://dev.to/petercour/distractions-whose-life-is-it-anyway-325h>

现在有些网站是为了浪费你的时间而设计的。你点击了一下，在你意识到之前，你已经浪费了一个小时或者更多的时间。

你可能有更好的事情要做。

那么如何找回自己的生活呢？

### 浏览器拦截

最简单的方法就是浏览器屏蔽。但这并没有帮助，因为作为一个有经验的开发者，你有很多方法可以快速浏览网页:chrome、firefox、falkon、safari、links、lynx。

您可能同时使用多台计算机。所以这种方法是无效的。

[![](img/0fe9c635562365f3a8003a24d94673c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SuEswNQx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jakeaw826xanheb6l59h.jpeg)

老实说:*浏览器屏蔽对于铁杆开发者来说并不有效。*

但是你可以试试 leechblock，stayfocusd 或者类似的工具

### 系统闭锁

一种方法是在/etc/hosts
中阻止这些站点，这样您就可以添加这样一行

```
127.0.0.1  twitter.com
127.0.0.1  facebook.com
127.0.0.1  news.ycombinator.com
127.0.0.1  digg.com
127.0.0.1  stumbleupon.com 
```

那会封锁网站。这在办公室的电脑上运行良好，但是如果你在家工作呢？

#### 火狐 DNS 缓存

现在，如果你使用 Firefox，它有自己的 DNS 缓存。打开 **about:config** ，搜索**network . dnscacheexpiration**，设置为 0。然后重启火狐。

[![](img/7d20536ca6411b55b6c2aa7d91276d27.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wy6fDuV9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252F55xNG9gNcRnd6%252Fgiphy.gif%26f%3D1)

### 巨蟒前来救援

可以编写一个在特定时间自动阻塞的 Python 脚本。类似这样的事情(我还没有广泛测试过):

```
#!/usr/bin/python3
import time
from datetime import datetime as dt
from datetime import datetime

#Path to the host file, redirect to local host, list of websits to block
host_path = "/etc/hosts"
redirect = "127.0.0.1"
website_list = ["www.netflix.com","www.facebook.com", "reddit.com","old.reddit.com","twitter.com","news.ycombinator.com","nu.nl"]

def is_between(time, time_range):
    if time_range[1] < time_range[0]:
        return time >= time_range[0] or time <= time_range[1]
    return time_range[0] <= time <= time_range[1]

while True:
    #Check for the current time

    what_time_is_it = datetime.now().strftime('%H:%M')
    print(what_time_is_it)
    if not is_between(what_time_is_it, ("09:00", "17:00")):
        print("Allowed")
        file = open(host_path,'w+')
        file.seek(0)
        file.truncate()        
    else:
        print("Work time now")
        file = open(host_path,'w+')
        file.seek(0)
        file.truncate()
        for website in website_list:
            print('block ' + website)
            line = redirect + "  " + website + "\n"
            file.write(line)

    time.sleep(5) 
```

现在这可以在后台运行。作为一个技术极客，你可以用 pkill 来关闭 Python。所以这也不是完美的。

相关链接:

*   [人类技术](https://humanetech.com/)
*   [注意力经济正在让我们破产](https://techcrunch.com/2017/07/30/the-attention-economy-created-by-silicon-valley-is-bankrupting-us/)
*   [Python 教程](https://pythonbasics.org/)
*   [时间和日期](https://pythonbasics.org/time-and-date/)