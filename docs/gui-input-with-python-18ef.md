# 使用 Python 的 GUI 输入

> 原文：<https://dev.to/petercour/gui-input-with-python-18ef>

EasyGUI 是一个简单 GUI 交互的易用界面，可以轻松抓取用户输入。它基于 tkinter，你可以用它进行快速的用户交互。

没听说过 EasyGUI？参见:[https://dev.to/petercour/easygui-with-python-example-45h5](https://dev.to/petercour/easygui-with-python-example-45h5)

因此，您只需几行代码就可以快速获取用户输入。

看看这个:

```
#!/usr/bin/python3
import easygui as g

def enterbox(msg,title):
    msg = g.enterbox(msg, title, default='',strip = False,image=None)
    print(msg)

enterbox(msg = 'content',title = 'title') 
```

然后:

[![](img/9fbe9175c2ec4f31230c43102ca4c962.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DGFYuk3K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/silpxl6nzpvkuq09qti8.png)

想问个数字？

```
#!/usr/bin/python3
import easygui as g

def integerbox(msg,title,minNum,maxNum):
    msg = g.integerbox(msg, title,default=0, lowerbound=minNum, upperbound=maxNum, image=None)
    print(msg)

integerbox('content', 'title',0,99) 
```

然后:

[![](img/3aa9f86e1341ee2a1e20bf29635f3d21.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wAk7VEFX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cemkyjaqxds39ax6do3x.png)

资源:

*   [Python Tkinter 视频教程](https://gumroad.com/l/ErLc)
*   [t inter stuff](https://pythonbasics.org/tkinter/)
*   [Python 教程](https://pythonbasics.org/)