# 用 virtualenv 设置 python 3.6 环境

> 原文：<https://dev.to/robbinespu/setup-python-3-6-enviroment-with-virtualenv-52lp>

我妻子让我把一些 PDF 文件转换成 Excell 电子表格。去年我做了同样的事情，用 python 写了一些小的脚本程序来完成转换，但是我不知道我现在把代码放在哪里了。可能是去年格式化我的笔记本电脑时丢失的..哈哈的笑😜

我是懒现在(猜猜我用 hànzì写了什么)我不记得我上次做得怎么样了。哈哈哈..凭借我的 Google-fu 技能，我发现 DfTables 提供了将文档转换为另一种格式的免费 API。不错！😬

Fedora 30 自带 python 3.7 安装。遗憾的是，dftables.com API 目前不能在 3.7 上工作😲但是不用担心，因为我们可以创建`virtualenv`来加载 Python 3.6

现在让我们将`virtualenv`安装到我们的工作站中，注意我不使用 sudo，因为我的帐户有管理权限，所以加载`--user`参数应该足够了。我不喜欢和须藤一起做事。

```
$ pip3 install --user virtualenv 
```

用 DNF 下载 python 3.6

```
$ sudo dnf install python36 
```

创建一些目录并`cd`到那个目录。比如我用的是`~/workstation/PDF-to-Excell`。我们将使用 python 3.6 来安装新的 python 环境:

```
$ cd ~/workstation/PDF-to-Excell
$ virtualenv --python=/usr/bin/python3.6 python36 
```

现在使用
进入环境

```
$ source python36/bin/activate 
```

您应该会得到这样的结果:

```
(python36) [rnm@robbinespu PDF-to-Excell] $ python --version
Python 3.6.9 
```

现在让我们试着安装 DfTables pips

```
(python36) [rnm@robbinespu PDF-to-Excell] $ pip install git+https://github.com/pdftables/python-pdftables-api.git
Collecting git+https://github.com/pdftables/python-pdftables-api.git
  Cloning https://github.com/pdftables/python-pdftables-api.git to /tmp/pip-req-build-yg3u92jx
  Running command git clone -q https://github.com/pdftables/python-pdftables-api.git /tmp/pip-req-build-yg3u92jx
Collecting requests (from pdftables-api==1.1.0)
  Downloading https://files.pythonhosted.org/packages/51/bd/23c926cd341ea6b7dd0b2a00aba99ae0f828be89d72b2190f27c11d4b7fb/requests-2.22.0-py2.py3-none-any.whl (57kB)
     |████████████████████████████████| 61kB 682kB/s 
Collecting certifi>=2017.4.17 (from requests->pdftables-api==1.1.0)
  Downloading https://files.pythonhosted.org/packages/69/1b/b853c7a9d4f6a6d00749e94eb6f3a041e342a885b87340b79c1ef73e3a78/certifi-2019.6.16-py2.py3-none-any.whl (157kB)
     |████████████████████████████████| 163kB 2.1MB/s 
Collecting urllib3!=1.25.0,!=1.25.1,<1.26,>=1.21.1 (from requests->pdftables-api==1.1.0)
  Downloading https://files.pythonhosted.org/packages/e6/60/247f23a7121ae632d62811ba7f273d0e58972d75e58a94d329d51550a47d/urllib3-1.25.3-py2.py3-none-any.whl (150kB)
     |████████████████████████████████| 153kB 10.6MB/s 
Collecting idna<2.9,>=2.5 (from requests->pdftables-api==1.1.0)
  Downloading https://files.pythonhosted.org/packages/14/2c/cd551d81dbe15200be1cf41cd03869a46fe7226e7450af7a6545bfc474c9/idna-2.8-py2.py3-none-any.whl (58kB)
     |████████████████████████████████| 61kB 10.6MB/s 
Collecting chardet<3.1.0,>=3.0.2 (from requests->pdftables-api==1.1.0)
  Downloading https://files.pythonhosted.org/packages/bc/a9/01ffebfb562e4274b6487b4bb1ddec7ca55ec7510b22e4c51f14098443b8/chardet-3.0.4-py2.py3-none-any.whl (133kB)
     |████████████████████████████████| 143kB 11.2MB/s 
Building wheels for collected packages: pdftables-api
  Building wheel for pdftables-api (setup.py) ... done
  Created wheel for pdftables-api: filename=pdftables_api-1.1.0-cp36-none-any.whl size=5779 sha256=3e99b8595945f9ec3251e8d27e329f14f6783353b7d98d0a3cbc5fb35de53fa0
  Stored in directory: /tmp/pip-ephem-wheel-cache-c1feg25z/wheels/49/66/c0/e4f778e772a77892a98b3aaaba300cbb27a29a07e0c225cd80
Successfully built pdftables-api
Installing collected packages: certifi, urllib3, idna, chardet, requests, pdftables-api
Successfully installed certifi-2019.6.16 chardet-3.0.4 idna-2.8 pdftables-api-1.1.0 requests-2.22.0 urllib3-1.25.3 
```

它工作了，很好！目前，我们已经隔离了 python 3.6 虚拟环境，并成功安装了 Pdftables 依赖项所需的 pip，现在，让我们来玩一玩吧😍