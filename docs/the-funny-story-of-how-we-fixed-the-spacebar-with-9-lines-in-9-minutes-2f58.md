# 我们如何在 9 分钟内用 9 行代码修复空格键的有趣故事

> 原文：<https://dev.to/abdurrahmaanj/the-funny-story-of-how-we-fixed-the-spacebar-with-9-lines-in-9-minutes-2f58>

这个短小精悍的故事讲述了在几个小时不舒服的打字后，我们如何最终找到了我们喜欢的东西。

我们每周都有 Python 会议，我们有这样一个人，他的空格键不工作，或者在你意想不到的时候工作，而且大部分时间都是关着的。

有一次运气真的来了，大约三个星期，我们忘记了它，直到空格键决定卷土重来。这个人使用的永久性解决方案是虚拟键盘。在打字之前，他会将鼠标指针放在键盘的空格键上，而不是按空格键，他会右击膝上型电脑的鼠标垫，点击虚拟空格键。我可以告诉你，打字没意思，编码也没意思！我们将添加用它做 git 推送，它真的是&^&^%*^%$%$。

正是在我们的实际 git 会议之后，我开始问自己，如果我们不能用 Python 解决这个问题，我们在教什么。我伸手去拿我的钥匙，我的第一个直觉是 PyAuto Gui。是的，那个人 5 分钟后就要离开了，他正在收拾他的东西...我必须马上把它做好！

我迷失在 PyAutoGUI 的 keys docs 中，我决定探索新的选择，就在那里，在狂野的蓝色中。看到一个包，键盘 gloups，应该想到了 XD。随着进口反重力笑话闪过我的脑海，我去了文档页面...是一个. md 文件。这个例子只展示了一个用例，我在挖掘、耕作、翻找...我是 ctrl+f 蛮搜索，直到我想出了一个无限循环，事件检测和键发送。酷！这个 api 真的很棒，因为你可以凭直觉猜到键名。做得好键盘开发🎉。

```
import keyboard  # using module keyboard 
while True:  # making a loop
    try:  # used try so that if user pressed other than the given key error will not be shown
        if keyboard.is_pressed('ctrl+alt+s'):  
            print('pressed')
            keyboard.write(' ')
    except:
        pass 
```

我没有发送文件，而是在那个人的笔记本电脑上用虚拟键盘输入程序，我没有时间为空格键设置那个疯狂的鼠标。我很快就进去了，手机短信的体验帮助了我。然后我们必须想出组合。在我的电脑上，ctrl+s 运行良好，但它是流行的保存命令，对他的电脑影响很大。我们试了 z+x，但是它在打那些字母。最后我们选定了 ctrl+alt+s。

然后关于部署，win8 自动链接 py 文件。在文件上点击一下，它就开始运行了。我们测试了一下，这个人非常喜欢，因为他厌倦了到处找老鼠。蛇最终吞下了老鼠。

你有一个关于一个小片段如何解决一个烦人问题的故事吗？下面分享一下！

([1] Mike Driscoll 有一个鼠标 Vs Python 的博客)

图片来自 unsplash