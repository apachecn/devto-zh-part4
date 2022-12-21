# Docker 快速入门中使用 Python 的 VS 代码

> 原文：<https://dev.to/siaarzh/vs-code-with-python-in-docker-quickstart-3ph4>

你喜欢 VS 代码？准备好更加热爱它吧！

最近，微软的优秀员工推出了一个新的扩展包，支持远程开发！不仅是 SSH，还可以将本地容器与单个容器或多个容器一起使用。

## 步骤

1.  安装 [VS 代码](https://code.visualstudio.com/)和[远程开发](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack)扩展包
2.  通过使用 VS Code Insiders 打开一个新目录来启动一个新项目，并使用您希望拥有的 Python 包创建一个 *requirements.txt* 文件，例如:

    ```
    django==2.2.2 
    ```

3.  `ctrl` + `shift` + `p`开始输入“remdev”，选择“rem-Containers:Add Dev Container Configuration Files ...”

4.  搜索“pypo”并选择“Python 3 & PostgreSQL”作为您的环境。这将创建一个*。devcontainer* 文件夹中的文件:

    *   devcontainer.json
    *   坞站-组合. yml
    *   Dockerfile
    *   noop.txt
5.  `ctrl` + `shift` + `p`开始输入“rerefo”，选择“远程-容器:重新打开容器中的文件夹”

6.  开始编码☕💻

### 关于开放端口的一个注记

那么，如何打开容器的端口呢？轻松点。VS 代码已经知道容器中哪些端口是打开的。

假设您用`python manage.py runserver`启动您的服务器，这将在您的容器中打开 127.0.0.1:8000 上的一个端口。VS 代码意识到了这一点，并会让您选择将系统端口转发到那个端口。

按下`ctrl` + `shift` + `p`，搜索“端口”，选择“远程-集装箱:集装箱转发端口……”。将出现一个开放端口列表，选择端口 8000。

## 源代码

[https://github.com/siaarzh/python-vscode-starter](https://github.com/siaarzh/python-vscode-starter)

## 参考文献

*   [集装箱远程开发](https://code.visualstudio.com/docs/remote/containers)带 VS 码
*   [Django 教程第 1 部分](https://docs.djangoproject.com/en/2.2/intro/tutorial01/)