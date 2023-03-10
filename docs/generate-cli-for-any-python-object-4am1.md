# 为任何 Python 对象生成 CLI

> 原文：<https://dev.to/petercour/generate-cli-for-any-python-object-4am1>

[![](img/4aa378c2787533815aac8d03fd6cd5f0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QEncn1kY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thumbs.gfycat.com/ImpracticalExaltedAntelopegroundsquirrel-size_restricted.gif)

您可以使用 fire 模块轻松生成命令行界面(CLI)。对于 Linux 或苹果终端。

这是针对一个 *Python 对象*，所以你需要知道[面向对象编程范例](https://pythonbasics.org/class/)。

```
class Bot():
    def hello(self, name):
        print("Hello " + name)

    def whatsup(self):
        print("All good!") 
```

然后导入 fire 模块，加载到主函数中。

```
import fire

class Bot():
    def hello(self, name):
        print("Hello " + name)

    def whatsup(self):
        print("All good!")

if __name__ == '__main__':
    fire.Fire(Bot) 
```

然后在命令行中:

```
λ  ~  python example.py
Type:        instance
String form: <__main__.Bot instance at 0x7fa7e1062320>

Usage:       example.py 
             example.py hello
             example.py whatsup 
```

[![](img/0eb6304e69e98ff6253e0b33146ff385.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6YLlp_w2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media.tenor.cimg/1e6772cb6fa4e46b51fd1db2fb26d723/tenor.gif)

需要用命令写参数:)

您可以:

```
λ  ~  python example.py hello bob
Hello bob 
```

调用另一个方法

```
λ  ~  python example.py whatsup  
All good!
λ  ~ 
```

是的，该类的所有方法都包括在内。

关于 Python 的更多信息:

*   [https://pythonbasics.org](https://pythonbasics.org)
*   [https://pythonprogramminglanguage.com/](https://pythonprogramminglanguage.com/)