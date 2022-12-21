# Python 清单:将包发布到 PyPi

> 原文：<https://dev.to/prahladyeri/python-checklist-publishing-a-package-to-pypi-4jlc>

*(最初发表于[https://github.com/prahladyeri/CuratedLists/](https://github.com/prahladyeri/CuratedLists/blob/master/check_lists/python-pypi.md))*

# 清单:将包发布到 PyPi

1.  如果您还没有帐户，请在 [PyPi](https://pypi.org/) 上注册一个。
2.  如果还没有安装`setuptools`和`twine`的话，使用`pip`安装它们。在你的源文件夹中创建一个`setup.py`，如下所示(查看[设置工具文档](https://setuptools.readthedocs.io)，了解更多详细的设置选项):

    ```
    # replace:
    # <your_package> with your actual package name.
    # <you> with your name.
    # <your_email> with your public email address.
    # <your_github_repo_url> with your github project's url.
    from setuptools import setup, find_packages

    s = setup(
        name="<your_package>",
        version="1.0.0",
        license="MIT",
        description="Foo App",
        url='<your_github_repo_url>",
        packages=find_packages(),
        install_requires=[],
        python_requires = ">= 3.4",
        author="<you>",
        author_email="<your_email>",
        ) 
    ```

3.  可选:如果这不是您的第一个版本，请提高版本号(并 git commit ):

    ```
    git add . && git commit -m "chore: released 1.0.1" && git push 
    ```

4.  从您的源文件夹运行`python setup.py sdist`来生成一个源分布。

5.  可选:用您的`gpg`签名对新生成的包进行签名:

    ```
    gpg -a --detach-sign dist/<your-package>-1.0.0.tar.gz 
    ```

6.  使用`twine`上传您的软件包:

    ```
    twine upload dist/<your-package>-1.0.0.tar.gz -u <your-pypi-username> -p <your-pypi-password> 
    ```

    如果您已经在包裹上签名，那么您也可以添加签名。asc 文件作为参数，如下所示:

    ```
    twine upload dist/<your-package>-1.0.0.tar.gz dist/<your-package>-1.0.0.tar.gz.asc -u <your-pypi-username> -p <your-pypi-password> 
    ```

7.  访问[https://pypi.org/project/your_package](https://pypi.org/project/your_package)以确认您的包已经上传。

8.  使用 pip 软件包管理器运行`pip install <your_package>`来验证软件包安装。

9.  可选:用新版本号标记提交:

    ```
    git tag "1.0.1"
    git push --tags 
    ```