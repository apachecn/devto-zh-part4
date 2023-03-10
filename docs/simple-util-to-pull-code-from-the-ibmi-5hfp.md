# 从 IBM i 中提取代码的简单实用程序

> 原文：<https://dev.to/barrettotte/simple-util-to-pull-code-from-the-ibmi-5hfp>

[![Alt Text](img/8cb5a0a3678d5502c6cb55d02ecd54f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cFYzor63--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0beve1mrakul3idi2avo.PNG)

在我正在做的一个非常愚蠢的副业项目中，我有一些 IBM i 代码，我想和其他一些东西一起保存在我的 git 存储库中。我可以使用集成文件系统(IFS ),但是说实话，我对 IBM 了解不多，还不能正确地使用它。

因此，与此同时，我有一个快速而肮脏的 python 脚本，它允许我基于一个配置文件 repo.json 提取多个源成员。

我的 repo.json 非常简单，但是它展示了一个基本的 IBM i 文件结构，可以用 FTP
在下面循环

```
{  "library":  "BOLIB",  "spfs":  [  {  "name":  "QRPGLESRC",  "extension":  "RPGLE",  "members":  [  {  "name":  "FIZZBUZZ"  }  ]  }  ],  "output":  "./"  } 
```

Enter fullscreen mode Exit fullscreen mode

## 基本想法

如果您通过 SSH 连接到 IBM i，那么您可以导航并找到您的源成员。例如，我将深入到我的 BOLIB 库中的 QRPGLESRC 文件。

[![Alt Text](img/064e35b8f43a9f8daaa36e95277a2ab5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Bb6K8MDi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/53fqzua58t0iuc0c56ah.PNG)

如你所见，有我的 QRPGLESRC 成员在闲逛。我的脚本所做的就是自动获取源成员。

## 代码

首先，我使用 python 标准库中的几个基本模块。

```
import ftplib  # easily setup FTP connection import json    # read in repo.json config file import getpass # used to discretely get password import os      # used to safely make directories for output 
```

Enter fullscreen mode Exit fullscreen mode

我加载我的配置文件，实例化我的 FTP 客户端，并从控制台获取主机名、用户名和密码

```
config = {}
with open("./repo.json", 'r') as f: 
  config = json.load(f)

ftp_client = ftplib.FTP()
host = input("Enter Host: ")
user   = input("Enter User: ")
password = getpass.getpass("Enter Password: ")
# FTP logic below... 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我用非常基本的错误处理设置我的 FTP 连接外壳

```
try:
  ftp_client.connect(host, timeout=10000)
  ftp_client.login(user, password)
  # The meat of the code here ... except ftplib.all_errors as e:
  print("Error occurred with FTP.\n" + str(e))
  exit(1)
except Exception as e:
  print("Some other error occurred\n" + str(e))
  exit(1)
finally:
  ftp_client.quit() 
```

Enter fullscreen mode Exit fullscreen mode

现在有趣的事情来了，发送 FTP RETR 命令从 IBM i 获取数据

```
lib = config['library'] # my example only has one library, so no looping 
for spf in config['spfs']:
  print("Fetching member(s) from {}/{}".format(lib, spf['name']))

  if not os.path.exists('./'+spf['name']): 
    os.makedirs(spf['name']) # make a directory based on source physical file name 
    for mbr in spf['members']:
      resp = []

      # The magic command to get data  ex: BOLIB/QRPGLESRC/FIZZBUZZ.mbr
      cmd = "RETR {}".format("/QSYS.lib/{}.lib/{}.file/{}.mbr").format(
        lib, spf['name'], mbr['name'])

      ftp_client.retrlines(cmd, resp.append) # run the command 
      # Create file based on specified extension ex: RPGLE
      filepath = spf['name'] + '/' + mbr['name'] + '.' + spf['extension']

      # Finally, write data to file
      with open(filepath, 'w+') as f:
        for line in resp: f.write(str(line) + '\n')

      print("  Saved " + filepath) 
```

Enter fullscreen mode Exit fullscreen mode

## 最终脚本

```
import ftplib  # easily setup FTP connection import json    # read in repo.json config file import getpass # used to discretely get password import os      # used to safely make directories for output 
config = {}
with open("./repo.json", 'r') as f: 
  config = json.load(f)

ftp_client = ftplib.FTP()
host = input("Enter Host: ")
user   = input("Enter User: ")
password = getpass.getpass("Enter Password: ")

try:
  ftp_client.connect(host, timeout=10000)
  ftp_client.login(user, password)

  lib = config['library'] # my example only has one library, so no looping 
  for spf in config['spfs']:
    print("Fetching member(s) from {}/{}".format(lib, spf['name']))

    if not os.path.exists('./'+spf['name']): 
      os.makedirs(spf['name']) # make a directory based on source physical file name 
    for mbr in spf['members']:
      resp = []

      # The magic command to get data  ex: BOLIB/QRPGLESRC/FIZZBUZZ.mbr
      cmd = "RETR {}".format("/QSYS.lib/{}.lib/{}.file/{}.mbr").format(
        lib, spf['name'], mbr['name'])

      ftp_client.retrlines(cmd, resp.append) # run the command 
      # Create file based on specified extension ex: RPGLE
      filepath = spf['name'] + '/' + mbr['name'] + '.' + spf['extension']

      # Finally, write data to file
      with open(filepath, 'w+') as f:
        for line in resp: f.write(str(line) + '\n')

      print("  Saved " + filepath)

except ftplib.all_errors as e:
  print("Error occurred with FTP.\n" + str(e))
  exit(1)
except Exception as e:
  print("Some other error occurred\n" + str(e))
  exit(1)
finally:
  ftp_client.quit() 
```

Enter fullscreen mode Exit fullscreen mode

## 文件被取出！

这存储在。/QRPGLESRC/FIZZBUZZ。RPGLE

```
 /free
       // The classic fizzbuzz program in RPGLE Free
       dcl-s num int(10);

       for num = 1 to 100;
           if %REM(num:3) = 0 and %REM(num:5) = 0;
               dsply ('num - ' + %char(num) + ' FIZZBUZZ');
           elseif %rem(num:3) = 0;
               dsply ('num - ' + %char(num) + ' FIZZ');
           elseif %rem(num:5) = 0;
               dsply ('num - ' + %char(num) + ' BUZZ');
           else;
               dsply ('num - ' + %char(num));
           endif;
       endfor;
       *INLR = *ON; 
```

Enter fullscreen mode Exit fullscreen mode

## 简单批处理脚本

在我的存储库中，我创建了一个小的批处理脚本，这样我就可以提取 IBM i 代码，并在一次调用中将它提交给我的存储库的其余部分。

```
@ECHO OFF
IF [%1] == [] GOTO NOMSG
python ibmi-pull.py && git add . && git commit -m "%~1" && git push origin master
GOTO END
:NOMSG
  ECHO "Enter the commit message!"
:END
PAUSE 
```

Enter fullscreen mode Exit fullscreen mode

再说一次，可能有一些更好的“工具”可以让你涉及到如果，但我只是还没有知识。

作为一个实验，我扩展了这个简单的脚本来获取整个库并生成一个基本的 git 库:
[https://github.com/barrettotte/IBMi-Lib-Repo](https://github.com/barrettotte/IBMi-Lib-Repo)

感谢阅读。