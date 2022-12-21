# PIP 中的包签名——它以一种迂回的方式工作

> 原文：<https://dev.to/prahladyeri/package-signing-in-pip-it-works-in-a-roundabout-sort-of-way-1kic>

几天前，我做了[这个开发来发布](https://dev.to/prahladyeri/why-doesn-t-the-python-package-manager-pip-have-package-signing-13ll)关于 Python 的 PIP 如何缺少 GPG 包签名。嗯，事实证明我错了！毕竟它有一个包签名过程，只是它是我迄今为止见过的最手工、最古老和最麻烦的安全实践之一。

昨天，当我登陆一位核心 python 开发者的博客文章时，我开始了解到这一点。为了以描述的方式测试包签名，我创建了一个名为 [siterank](https://github.com/prahladyeri/siterank) 的测试包，这是一个获取给定网站的 alexa 排名的小脚本。

首先，你必须只使用 [twine](https://github.com/pypa/twine) 上传一个签名包到 PyPi，因为只有 twine 有这个功能。其次，他们的文档似乎过时了，因为有些论点似乎行不通。例如，用于显式指定签名文件的`--sign`参数对我不起作用:

```
-s, --sign            Sign files to upload using GPG 
```

Enter fullscreen mode Exit fullscreen mode

起作用的是上传包文件和生成的签名文件(*。asc)这样连续地:

```
twine upload siterank-0.2.tar.gz siterank-0.2.tar.gz.asc 
```

Enter fullscreen mode Exit fullscreen mode

还要注意，在 PyPi 上的[包页面上，您将看不到上传的签名文件。但是有两种不同的方法来验证签名:](https://pypi.org/project/siterank/#files)

1.  首先，你可以使用 [PyPi JSON API](https://pypi.org/pypi/siterank/json) 。它包含了所有上传版本的`JSON`格式，注意在第二个包版本中，`has_sig`属性被设置为 true！
2.  第二种方法是将`.asc`扩展名添加到您的安装文件的链接中，在我的例子中是:[https://files . python hosted . org/packages/16/F9/1 dfce 544610 b 9 dcbbfcb 4095 c8e 143 c 6 CFD 54 b 4371 cced 3 f 73 df0 a 99926/site rank-0.2 . tar . gz . ASC](https://files.pythonhosted.org/packages/16/f9/1dfce544610b9dcbbfcb4095c8e143c6cfd54b4371ccedc3f73df0a99926/siterank-0.2.tar.gz.asc)

因此，有人想验证这个包是否确实是我写的，可以提取这个`.asc`文件，并将其与我的 GPG 公钥(ID `E12979BA15FDE7FD` -也可以通过运行`gpg --search-keys prahladyeri@yahoo.com`找到)进行匹配。

这种迂回的求证方式就不用说了，繁琐，繁琐。这个过程应该是无缝的和自动化的，并且像 apt 和 dnf 一样包含在`pip`工作流本身中。唯一可能的问题是数百万开发者上传他们的包到 PyPi，每个人可能都不想(或者缺乏知识)使用 GPG 密钥签名。因此，签名可以保持为可选的(就像现在一样)，但是验证选项应该存在于已签名的包中，因为它可以确保包的安全性和完整性。

另一个问题是收养。我从那个 JSON API 中注意到了几个流行的项目，像`requests`、`nltk`、`flask`等。还没有在他们的包裹上签名。越来越多的开发者推出签名包，从而使 PyPi 成为一个更安全的安装包和分发包的环境，这一点很重要。

安全和隐私可能是我们这个时代讨论最多的话题之一。全球各种各样的人和公司试图通过对平民隐藏尽可能多的事情来妥协这些。有鉴于此，安全和隐私应该在开源项目中被给予最高的优先权。我希望 Python 项目能够理解这些顾虑，并为此做些什么。