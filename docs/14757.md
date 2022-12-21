# 用 Python 使日志丰富多彩

> 原文：<https://dev.to/0xkoji/make-log-colored-with-python-1fli>

### 安装色彩

```
$ pip install colorama 
```

Enter fullscreen mode Exit fullscreen mode

### 代码

`test.py`

```
from colorama import Fore, Back, Style

print('===========================')
print(Fore.GREEN + "green string")
print(Style.DIM + "dim: test")
print(Style.BRIGHT + "BRIGHT: test")
print(Style.NORMAL + "NORMAL: test")
print(Style.RESET_ALL + "RESET_ALL")
print(Back.GREEN + 'green')
print(Style.RESET_ALL)
print('===========================')
print(Fore.RED + "red string")
print(Style.DIM + "dim: test")
print(Style.BRIGHT + "BRIGHT: test")
print(Style.NORMAL + "NORMAL: test")
print(Style.RESET_ALL + "RESET_ALL")
print(Back.RED + 'red')
print(Style.RESET_ALL)
print('===========================')
print(Fore.YELLOW + "yellow string")
print(Style.DIM + "dim: test")
print(Style.BRIGHT + "BRIGHT: test")
print(Style.NORMAL + "NORMAL: test")
print(Style.RESET_ALL + "RESET_ALL")
print(Back.YELLOW + 'yellow')
print(Style.RESET_ALL)
print('===========================')
print(Fore.BLUE + "blue string")
print(Style.DIM + "dim: test")
print(Style.BRIGHT + "BRIGHT: test")
print(Style.NORMAL + "NORMAL: test")
print(Style.RESET_ALL + "RESET_ALL")
print(Back.BLUE + 'blue')
print(Style.RESET_ALL) 
```

Enter fullscreen mode Exit fullscreen mode

### 结果

[![](img/977bfff4a92df8be7b7b4bc47b2b6a0a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eUipa1wB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ip4toepazo020kd7pfpc.png)