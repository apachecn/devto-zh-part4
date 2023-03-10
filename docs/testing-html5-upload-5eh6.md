# 测试 HTML5 上传

> 原文：<https://dev.to/dowenb/testing-html5-upload-5eh6>

## 背景

我正在测试的一个项目涉及 HTML5 文件上传。我曾认为“Browser”a 文件提示会阻止我使用 Selenium 测试框架来自动化这个过程。然而，事实证明，就像许多事情一样，这是一个“已解决的问题”。

简而言之，您可以识别“浏览器”按钮输入元素，并使用“发送键”功能来提供您想要上传的文件的路径。然后像往常一样使用“Click”命令触发提交拼接。

## 设置虚拟环境，安装硒基

我在 PowerShell 提示符下输入了以下命令，我已经安装了 Python 3、pip 和 virtualenv。

```
cd  e:\dev  virtualenv  uploadtestenv  .\uploadtestenv\Scripts\activate.ps1  pip  install  seleniumbase  seleniumbase  install  chromedriver  seleniumbase  mkdir  file_upload_test  cd  file_upload_test 
```

Enter fullscreen mode Exit fullscreen mode

有 SeleniumBase 帮助文档:[https://github . com/selenium base/selenium base/tree/master/Help _ docs](https://github.com/seleniumbase/SeleniumBase/tree/master/help_docs)

## [T1】file _ upload _ test . py](#fileuploadtestpy)

运行这个例子——将下面的内容复制到一个名为`file_upload_test.py`
的文件中

```
from seleniumbase import BaseCase
from time import  time
from os import remove

class MyTestClass(BaseCase):

    def test_basic(self):
        # setup testdata to be uploaded 
        # time based filename generated for this run only
        filename = str(int(round(time() * 1000))) + ".txt"

        # path with filename
        path = "E:\\dev\\file_upload_test\\" + filename

        f= open(filename,"w+")
        for i in range(10):
            f.write("This is line %d\r\n" % (i+1))
        f.close()

        # SelinumBase test
        self.open("https://the-internet.herokuapp.com/upload")
        self.send_keys('#file-upload', path)
        self.click('#file-submit')
        self.assert_exact_text(filename, '#uploaded-files')

        # Clean up testdata
        remove(path) 
```

Enter fullscreen mode Exit fullscreen mode

运行测试(同样是 PowerShell，相同的文件夹):

```
pytest .\file_upload_test.py --demo_mode 
```

Enter fullscreen mode Exit fullscreen mode

## 谢谢

这个原型例子利用了美妙的“互联网”资源上的 HTML5 上传器，作者是。帮助测试硒测试的非常有用的资源。来看看:【https://the-internet.herokuapp.com】T2

测试数据创建示例基于一篇 Guru99 文章: [Python 文件处理:创建、打开、追加、读取、写入](https://www.guru99.com/reading-and-writing-files-in-python.html)

克里斯蒂娜·莫里路拍摄的封面照片《巨蟒之书》