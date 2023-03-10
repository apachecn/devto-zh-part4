# 使用 Python 脚本获取 Linux 中的系统信息

> 原文：<https://dev.to/anuragrana/getting-system-information-in-linux-using-python-script-2g3f>

在 Ubuntu 中查找系统信息，如处理器的数量和类型、内存使用情况、正常运行时间等非常容易。

您可以使用像 free -m、uname -a 和 uptime 这样的 Linux 系统命令来查找这些细节。但是那样做一点也不好玩。

如果你喜欢用 python 编程，你想用 python 做任何事情。因此，我们将看到如何使用 python 程序找到这些信息。并且在这个过程中除了 python 还会学到一些关于 Linux 系统的东西。

为了找到一些细节，我们将使用 python 模块`platform`。我们将使用 python3 解释器运行这个脚本，并且这个脚本已经在 Ubuntu 16.04 上测试过了。

**一般信息:**
So 平台模块用于访问底层平台的标识数据。
我们将使用本模块中的一些方法。

要得到架构，调用架构方法。它返回一个元组(位，链接)。

要获得 Linux 发行版，调用`dist()`或`linux_distribution()`方法。它还返回一个元组。

```
import platform

# Architecture print("Architecture: " + platform.architecture()[0])

# machine print("Machine: " + platform.machine())

# node print("Node: " + platform.node())

# system print("System: " + platform.system())

# distribution dist = platform.dist()
dist = " ".join(x for x in dist)
print("Distribution: " + dist) 
```

现在，要获得其他信息，我们需要进入您系统的`/proc/`目录。如果你看一下文件，你会知道系统在哪里存储这种类型的信息。

**处理器信息:**
处理器信息存储在`cpuinfo`文件中。读取文件并计算处理器的编号和型号名称。

```
# processor print("Processors: ")
with open("/proc/cpuinfo", "r")  as f:
    info = f.readlines()

cpuinfo = [x.strip().split(":")[1] for x in info if "model name"  in x]
for index, item in enumerate(cpuinfo):
    print("    " + str(index) + ": " + item) 
```

**内存使用:**
内存详细信息存储在`/proc/meminfo`文件中。第一行是系统中的总内存，第二行是当前可用的空闲内存。

```
# Memory print("Memory Info: ")
with open("/proc/meminfo", "r") as f:
    lines = f.readlines()

print("     " + lines[0].strip())
print("     " + lines[1].strip()) 
```

[继续阅读.....](https://www.pythoncircle.com/post/535/python-script-9-getting-system-information-in-linux-using-python-script/)

**更多来自作者:**

*   [https://dev . to/anuragrana/collecting-million-website-links-3jc 5](https://dev.to/anuragrana/collecting-one-million-website-links-3jc5)
*   [https://dev . to/anuragrana/wishing-merry-Christmas-in-pythonic-way-47mk](https://dev.to/anuragrana/wishing-merry-christmas-in-pythonic-way-47mk)
*   [https://www . python circle . com/post/518/scraping-10000-tweets-in-60 seconds-using-celery-rabbit MQ-and-docker-cluster-with-rotating-proxy/](https://www.pythoncircle.com/post/518/scraping-10000-tweets-in-60-seconds-using-celery-rabbitmq-and-docker-cluster-with-rotating-proxy/)
*   [https://www . python circle . com/post/485/python-script-8-validating-credit-card-number-luhns-algorithm/](https://www.pythoncircle.com/post/485/python-script-8-validating-credit-card-number-luhns-algorithm/)