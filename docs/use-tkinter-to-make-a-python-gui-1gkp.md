# 用 Tkinter 做一个 Python GUI？

> 原文：<https://dev.to/petercour/use-tkinter-to-make-a-python-gui-1gkp>

Python 有很多 GUI 模块。其中一个就是 Tkinter。那么 Tkinter 呢？

也许吧。如果你的程序不需要复杂的 GUI 部件或者本地 GUI。

[![](img/033ee4054d6c080a518df9df45463b2b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--16DzNyZG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wavv02lsdg1903dzgw8x.jpeg)

如果你需要很多小部件和原生操作系统外观，使用 PyQt5 模块可能会更好。

Tkinter 已经存在很久很久了。它包含基本的小部件

*   按钮、复选按钮、菜单按钮、单选按钮
*   帆布
*   进入
*   基本框架
*   标签，标签框架
*   列表框
*   菜单...诸如此类。

因此您可以构建基本的 GUI 界面。但是它没有一个庞大的小部件列表。

另一方面，它有一个简单的学习曲线。hello world 程序看起来像这样:

```
#!/usr/bin/python3
import tkinter as tk

class Application(tk.Frame):
    def __init__(self, master=None):
        super().__init__(master)
        self.master = master
        self.pack()
        self.create_widgets()

    def create_widgets(self):
        self.hi_there = tk.Button(self)
        self.hi_there["text"] = "Hello World\n(click me)"
        self.hi_there["command"] = self.say_hi
        self.hi_there.pack(side="top")

        self.quit = tk.Button(self, text="QUIT", fg="red",
                          command=self.master.destroy)
        self.quit.pack(side="bottom")

    def say_hi(self):
        print("hi there, everyone!")

root = tk.Tk()
app = Application(master=root)
app.mainloop() 
```

相关链接:

*   [t inter 模块](https://docs.python.org/3/library/tkinter.html)
*   [Tkinter 简介](https://pythonbasics.org/tkinter/)
*   [t inter 课程和示例](https://gumroad.com/l/ErLc)