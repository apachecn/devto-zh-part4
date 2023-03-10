# 用 tkinter 绘画

> 原文：<https://dev.to/petercour/paint-with-tkinter-3a7j>

Tkinter 是一个构建图形用户界面(GUI)的模块。那是桌面软件，可能就是你现在正在使用的那个。

大多数桌面软件都是关于按钮、文本字段之类的东西。油漆呢？有趣的东西？如何创建一个绘画窗口？

[![](img/036e4600ecb83f6c25423dc756aad854.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KkKYH7a---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lf1vjak4qi3afy0el1vd.png)

在下面的代码中，创建了一个画布部件，您可以在上面绘画。

一个重要的方法是

```
#!/usr/bin/python3
def test_drag(self,event):
   self.canvas.create_oval(event.x,event.y,event.x+1,event.y+1) 
```

这在鼠标位置上创建了一个椭圆形。+1 表示大小。你可以改变尺寸，比如

```
#!/usr/bin/python3
def test_drag(self,event):
   self.canvas.create_oval(event.x,event.y,event.x+10,event.y+10) 
```

好的，那么代码看起来像什么？

```
#!/usr/bin/python3
from tkinter import *

class application(Frame):
    def __init__(self,master):
        super().__init__(master)
        self.master=master
        self.pack()
        self.createWidget()

    def createWidget(self):

        self.canvas = Canvas(
        self,width=200,height=200,bg='white')
        self.canvas.pack()
        self.canvas.bind('<Button-1>',self.mouseTest)
        self.canvas.bind('<B1-Motion>',self.test_drag)
        self.canvas.bind('<KeyPress>',self.keyboard_test)
        self.canvas.bind('<KeyPress-a>',self.press_a_test)
        self.canvas.bind('KeyRelease-a',self.release_a_test)

    def mouseTest(self,event):
        print('{0},{1}'.format(event.x,event.y))
        print('{0},{1}'.format(event.x_root,event.y_root))
        print('{0}'.format(event.widget))

    def test_drag(self,event):
        self.canvas.create_oval(event.x,event.y,event.x+1,event.y+1)

    def keyboard_test(self,event):
        print('keycode:{0},char:{1},keysym:{2}'.format(event.keycode,event.char,event.keysym))

    def press_a_test(self,event):
        print('press a')

    def release_a_test(self):
        print('release a')

if __name__ == '__main__':
    root = Tk()
    root.geometry('200x200')
    app=application(root)
    root.mainloop() 
```

喜欢画画！

[![](img/5e6b997e0f42bb6a490388d76a72b666.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9zB35-Zp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/73in34p72x2jcmm932on.png)

了解更多信息:

*   [Tkinter 教程](https://pythonbasics.org/tkinter/)
*   [Python 教程](https://pythonbasics.org/)
*   [t inter 课程](https://gumroad.com/l/ErLc)