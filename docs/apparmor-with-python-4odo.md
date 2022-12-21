# Python 的 AppArmor

> 原文：<https://dev.to/petercour/apparmor-with-python-4odo>

AppArmor 是一个 Linux 内核安全模块，它允许系统管理员用每个程序的配置文件来限制程序的能力。

默认运行在 Ubuntu Linux 和 Suse Linux 上。其他 Linux 发行版通常有其他解决方案。

AppArmor 允许您限制进程。每个程序都有一个配置文件。配置文件定义了程序可以做什么。

它可以设置如下选项:

*   允许/拒绝网络访问等功能
*   允许/拒绝原始套接字访问
*   读取、写入或执行文件的权限

[![](img/cd4ae96160b3aee68536328e3117c9ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O4blfTil--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252F10WT1xxLoFcFc4%252Fgiphy.gif%26f%3D1)

### Python 接口

您可以使用 Python 与 AppArmor 进行交互。我设置了一个非常简单的程序来和 apparmor 交互。

我的想法是只使用子流程来捕获数据:

```
#!/usr/bin/python3
import subprocess
import json
import os

def profiles():
    result = subprocess.run(['apparmor_status', '--json'], stdout=subprocess.PIPE)    
    data = result.stdout.decode('utf-8')
    aa_status = json.loads(data)
    return aa_status 
```

这将运行命令 apparmor_status 并解析它的 json 输出。程序的其余部分以类似的方式制作。

```
#!/usr/bin/python3
# apparmor interface

import subprocess
import json
import os

def profiles():
    result = subprocess.run(['apparmor_status', '--json'], stdout=subprocess.PIPE)    
    data = result.stdout.decode('utf-8')
    aa_status = json.loads(data)
    return aa_status

def unconfined():
    result = subprocess.run(['aa-unconfined', '--paranoid'], stdout=subprocess.PIPE)
    data = result.stdout.decode('utf-8')
    lines = data.split("\n")
    apps = []

    for line in lines:
        app = line.split(" ")
        if len(app) > 1:
            apps.append( (app[0],app[1]) )

    return apps

def complain(profile):
    os.system("sudo aa-complain " + profile)

def enforce(profile):
    os.system("sudo aa-enforce  " + profile)

def disable(profile):
    os.system("sudo ln -s " + profile + " /etc/apparmor.d/disable/")

# List all profiles
aa_status = profiles()
print('version: ' + aa_status['version'])

for profile in aa_status['profiles']:
   status = aa_status["profiles"][profile]
   print(status + " " + profile)

# Get unconfined profiles
apps = unconfined()
for app in apps:
    print(app[1]) 
```

它只是与命令行界面进行交互。仍然是实验性的，但似乎是可行的。

[![](img/8b9c27768d9fe2a5eb9f5a58364d6704.png)](https://i.giphy.com/media/12OIWdzFhisgww/giphy.gif)

了解更多信息:

*   [https://www.udemy.com/apparmor-linux-security/](https://www.udemy.com/apparmor-linux-security/)
*   [https://wiki.ubuntu.com/AppArmor](https://wiki.ubuntu.com/AppArmor)
*   [https://pythonprogramminglanguage.com](https://pythonprogramminglanguage.com)
*   [https://pythonbasics.org](https://pythonbasics.org)