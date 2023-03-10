# Python 自定义 CLI 指南

> 原文：<https://dev.to/mesadhan/python-custom-cli-guide-cop>

# CLI 实施指南

1.步骤:创建`inktechs`包

创建'**初始化**。py”文件。现在
应该是空的，创建`__main__.py`文件

```
def main():
    print('Hello! from inktechs cli')

if __name__ == '__main__':
    main() 
```

Enter fullscreen mode Exit fullscreen mode

2.步骤 3:然后，创建`setup.py`文件，并为您的 CLI 定义元

```
from setuptools import setup

setup(
    name='inktechs-cli',
    version='0.1.0',
    packages=['inktechs'],
    author="Md. Sadhan Sarker",
    author_email="cse.sadhan@gmail.com",
    description="This is an Example Package",
    keywords="keyword1 keyword2",
    entry_points={
        'console_scripts': [
            'inktechs = inktechs.__main__:main'
        ]
    }) 
```

Enter fullscreen mode Exit fullscreen mode

3 .最后创建`install.sh`文件

```
pip install -e . 
```

Enter fullscreen mode Exit fullscreen mode

4.现在，运行 CLI，从终端点击您的软件包名称

```
inktechs -c ./home/file.conf --o ./home/text.file 
```

Enter fullscreen mode Exit fullscreen mode

> 输出:如下所示

```
Hello! from inktechs cli

Update configuration as following: ./home/file.conf

Generate output as following ./home/text.file 
```

Enter fullscreen mode Exit fullscreen mode

5.如果要卸载软件包，请根据您的 cli 软件包名称

```
pip uninstall inktechs-cli 
```

Enter fullscreen mode Exit fullscreen mode

# 
  
源代码:

*   [https://github.com/mesadhan/inktechs-cli.git](https://github.com/mesadhan/inktechs-cli.git)

# 参考文献

*   [https://docs.python.org/3/library/getopt.html](https://docs.python.org/3/library/getopt.html)
*   [https://packaging . python . org/guides/installing-using-pip-and-virtual-environments/](https://packaging.python.org/guides/installing-using-pip-and-virtual-environments/)
*   [https://setup tools . readthedocs . io/en/latest/setup tools . html #开发模式](https://setuptools.readthedocs.io/en/latest/setuptools.html#development-mode)