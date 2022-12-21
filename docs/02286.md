# 答案:寻找斜率变化的点作为自由参数- Python

> 原文：<https://dev.to/towry/answer-finding-the-point-of-a-slope-change-as-a-free-parameter-python-2bpd>

<header>

# ![](img/01c67cd39e9a8e551fcb75e1091225e9.png) [ 回答 re:寻找斜率变化的点作为自由参数——Python](https://stackoverflow.com/questions/45063260/finding-the-point-of-a-slope-change-as-a-free-parameter-python/45063636#45063636)

Jul 12 '17[![](img/e3f0373ec76330150a340eacd410b600.png)7![](img/f7bb704c8c93dfae05d2b57012ed2754.png)](https://stackoverflow.com/questions/45063260/finding-the-point-of-a-slope-change-as-a-free-parameter-python/45063636#45063636) </header>

这是你的数据图:

[![enter image description here](img/689e6c3935f4deba824e24ffef6ed1fd.png)](https://i.stack.imgur.com/JK0Sf.png)

你需要找到两个斜率(==取两个导数)。首先，求每两点之间的斜率(使用`numpy`):

```
import numpy as np 
x = np.array([1, 2, 3, 4, 5, 6, 7, 8, 9, 10],dtype=np.float)
y = np.array([1, 2, 3, 4,
```

…<button class="ltag__stackexchange--btn" type="button">[Open Full Answer](https://stackoverflow.com/questions/45063260/finding-the-point-of-a-slope-change-as-a-free-parameter-python/45063636#45063636)</button>