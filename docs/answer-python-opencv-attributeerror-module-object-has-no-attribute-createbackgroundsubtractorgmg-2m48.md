# 答案:python-opencv attribute 错误:“模块”对象没有属性“createBackgroundSubtractorGMG”

> 原文：<https://dev.to/udayposia/answer-python-opencv-attributeerror-module-object-has-no-attribute-createbackgroundsubtractorgmg-2m48>

<header>

# ![](img/01c67cd39e9a8e551fcb75e1091225e9.png) [ 答案re:python-opencv attribute error:' module '对象没有属性' createbackgroundsubtractormgm '](https://stackoverflow.com/questions/21626619/python-opencv-attributeerror-module-object-has-no-attribute-createbackground/54662758#54662758)

Feb 13 '19[![](img/e3f0373ec76330150a340eacd410b600.png)2![](img/f7bb704c8c93dfae05d2b57012ed2754.png)](https://stackoverflow.com/questions/21626619/python-opencv-attributeerror-module-object-has-no-attribute-createbackground/54662758#54662758) </header>

bgsegm 在 opencv 的 contrib 模块中，但是更新后我不确定。

但是，如果您还没有构建 contrib 模块:

```
pip install opencv-contrib-python 
```

在执行安装过程时，确保没有导入 cv2 的控制台正在运行。以管理身份运行`cmd`

成功了…

<button class="ltag__stackexchange--btn" type="button">[Open Full Answer](https://stackoverflow.com/questions/21626619/python-opencv-attributeerror-module-object-has-no-attribute-createbackground/54662758#54662758)</button>