# 用 Python 压缩

> 原文：<https://dev.to/petercour/zip-with-python-13lj>

[![zip](img/7bb6bcb2d2b698e5a9250f69fdb45606.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U-zrVjsd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.giphy.com/media/9hSsOshicxQHe/giphy.webp)

你可以用 Python 压缩和解压 zip 文件。什么？是..你可以。有一个名为 zipfile 的模块可以让你做到这一点

添加单个文件

```
#!/usr/bin/python3
import zipfile
f = zipfile.ZipFile('archive.zip','w',zipfile.ZIP_DEFLATED)
f.write('file_to_add.py')
f.close() 
```

### 创建一个 zip 程序

现在，您可以在 fire 模块的命令行界面中做到这一点

```
#!/usr/bin/python3
import fire
import zipfile

class ZipApp(object):

    def create_zip(self, filename, yourfile):
        f = zipfile.ZipFile(filename,'w',zipfile.ZIP_DEFLATED)
        f.write(yourfile)
        f.close()

    def unzip(self,filename):
        zfile = zipfile.ZipFile(filename,'r')
        for filename in zfile.namelist():
            data = zfile.read(filename)
            file = open(filename, 'w+b')
            file.write(data)
            file.close()

if __name__ == '__main__':
    fire.Fire(ZipApp) 
```

然后只需使用终端创建您的拉链:

```
python3 zip.py create-zip example.zip zip.py
python3 zip.py unzip example.zip 
```

你会用这个来代替 Linux 中的 unzip 吗？可能不会，但是用代码创建 zip 很有趣。

[![](img/4b810d4a3af636f385105d27a0f3f3a5.png)](https://i.giphy.com/media/l2Sqhi8BhHePhHohG/giphy.gif)

根据您的程序，将数据存储到 zip 文件或提取 zip 文件可能会很有用。

学习 Python:

*   [https://pythonprogramminglanguage.com/](https://pythonprogramminglanguage.com/)
*   [https://pythonbasics.org/](https://pythonbasics.org/)