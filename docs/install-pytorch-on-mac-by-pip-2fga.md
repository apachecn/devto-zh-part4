# 通过 pip 在 Mac 上安装 PyTorch

> 原文：<https://dev.to/berry_clione/install-pytorch-on-mac-by-pip-2fga>

# 概述

1.  在 Mac OS X 10.14.4 上安装 PyTorch
2.  检查它是否工作。

* * *

# 环境

*   麦克 OS X 莫哈韦
*   哦-我-zsh
*   [家酿 2.1.6 (git 修订版 0d363)](https://dev.to/berry_clione/install-homebrew-on-macos-mojave-4m3m)
*   Python 3.7.2
*   蟒蛇 3-5.3.1
*   pip 19.1.1

* * *

# 如何安装 PyTorch

*   PyTorch 官方表示，如果你已经有了 Anaconda，你可以通过 conda 安装 PyTorch。
*   但是 conda 可能只获取已经构建的包，而 pip 可能在获得与安装环境兼容的轮子后构建包，因此性能可能会有所不同。
    *   我不太确定。日志可能会说得很清楚。
*   然后，使用 pip 安装 PyTorch

```
$ pip3 install torch torchvision
Collecting torch
  Downloading https://files.pythonhosted.org/packages/95/80/0851d6088bb054be3ddf47bfde1aedb4e1cbd170cf8e1c60cad321b97162/torch-1.1.0.post2-cp37-none-macosx_10_7_x86_64.whl (88.1MB)
     |████████████████████████████████| 88.1MB 604kB/s
Collecting torchvision
  Downloading https://files.pythonhosted.org/packages/af/7c/247d46a1f76dee688636d4d5394e440bb32c4e251ea8afe4442c91296830/torchvision-0.3.0-cp37-cp37m-macosx_10_7_x86_64.whl (231kB)
     |████████████████████████████████| 235kB 894kB/s
Requirement already satisfied: numpy in /Users/foo/.pyenv/versions/anaconda3-5.3.1/lib/python3.7/site-packages (from torch) (1.15.1)
Requirement already satisfied: pillow>=4.1.1 in /Users/foo/.pyenv/versions/anaconda3-5.3.1/lib/python3.7/site-packages (from torchvision) (5.2.0)
Requirement already satisfied: six in /Users/foo/.pyenv/versions/anaconda3-5.3.1/lib/python3.7/site-packages (from torchvision) (1.11.0)
Installing collected packages: torch, torchvision
Successfully installed torch-1.1.0.post2 torchvision-0.3.0 
```

*   检查 PyTorch 是否工作正常。

```
from __future__ import print_function
import torch
x = torch.rand(5, 3)
print(x) 
```

*   结果

```
tensor([[0.7747, 0.9627, 0.1806],
        [0.9219, 0.1319, 0.9026],
        [0.6719, 0.6495, 0.5341],
        [0.6629, 0.4546, 0.7309],
        [0.3577, 0.8918, 0.8544]]) 
```

*   检查 GPU CUDA 是否可用

```
import torch
print(torch.cuda.is_available()) 
```

*   如果 CUDA 不可用，结果如下所示

```
False 
```