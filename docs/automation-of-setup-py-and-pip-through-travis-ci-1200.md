# 通过 Travis CI 实现 setup.py 和 pip 的自动化？

> 原文：<https://dev.to/prahladyeri/automation-of-setup-py-and-pip-through-travis-ci-1200>

Travis CI 对于[单开发者 github 项目](https://github.com/prahladyeri)有必要(甚至有帮助)吗？

我目前的工作方式是运行`setup.py sdist`，它为我的包生成 tar 文件，然后我使用`twine`将这些文件上传到 PyPi。

我想知道如何使用 travis-ci 进行一个简单的项目。有没有一个简单的 hello world python 项目可以做到这一点？它的文档似乎很复杂，而且似乎有多种使用 tox 配置`travis.yaml`的方式，等等。

主要是，我想知道:

1.  travis 会自动为我运行`setup.py sdist`吗？
2.  它如何知道何时触发构建？
3.  它把释放 tarballs 放在哪里？
4.  它也能让 github 自动发布吗？

我也在考虑在我的包中加入单元测试。是推荐还是有其他选择？