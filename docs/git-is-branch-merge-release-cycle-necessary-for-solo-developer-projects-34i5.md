# Git:分支/合并/发布周期对于 solo 开发者项目是必要的吗？

> 原文：<https://dev.to/prahladyeri/git-is-branch-merge-release-cycle-necessary-for-solo-developer-projects-34i5>

我是一名独立的自由开发者，也通过维护 github 上的[项目为开源做出贡献。与大多数其他项目所遵循的(分支、合并和发布)不同，我遵循一个简单的“标记和发布”工作流程。](https://github.com/prahladyeri/)

例如，当新代码准备好并经过测试后，我会做以下事情:

1.  增加`__init__.py`中的版本号。
2.  通过运行`setup.py`创建一个源代码分发包(`.tar.gz`)。
3.  发布到 [PyPi](https://pypi.org/user/prahladyeri/) 然后最后标记代码到版本(比如“1.0.3”等。).

比如你可以看到项目 [cfgsaver](https://github.com/prahladyeri/cfgsaver) ，最新的标签是“1.0.2”在下一个版本会更新为“1.0.3”。

我如何通过遵循分支/合并/发布周期来改进工作流程？你认为哪种工艺更好，为什么？