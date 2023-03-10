# 每个目标的 SwiftLint 配置

> 原文：<https://dev.to/elpassion/swiftlint-config-per-target-1ija>

> 开放源代码的激情。

在我们交付尽可能高质量产品的使命中，EL Passion 的 iOS 团队依靠林挺工具，如`SwiftLint`来修复常见的代码结构&格式问题。

然而，在一些场景中，我们不容易使用工具为我们工作。我们在使用以下目录结构开发[乙烯基商店移动应用](https://github.com/elpassion/VinylShop)时遇到了这个问题:

```
Project /
├── Project.xcodeproj
└── Sources /
    └── Controllers /
       └── Login /
           ├── LoginViewController.swift
           └── LoginViewControllerSpec.swift 
```

我们无法轻松地将 SwiftLint 配置为对生产文件和测试文件使用不同的配置。

## 金丝燕

幸运的是，我们已经能够通过实现一个简单的 Python 脚本来修复这个问题，该脚本读取`.pbxproj`文件的内容，并在林挺之前修改 SwiftLint 配置中包含的文件。该脚本分为两个简单的文件:

*   [lint.py](https://github.com/elpassion/swiftlint-runner/blob/master/commands/lint.py) ，
*   xcprox . py。

更好的是，我们已经发布了 PyPI 包形式的脚本，所以您也可以在自己的项目中使用它。跟跑步一样简单:

```
pip install swiftlint-runner 
```

并添加适当的构建阶段:

```
slrunner <project_name>.xcodeproj <target_name> .<swiftlint_config_file_name>.yml 
```

您可以在[El passion/swift lint-runner](https://github.com/elpassion/swiftlint-runner)资源库中找到更多详细信息。

## 执照

SwiftLint runner 由 EL Passion 与❤️一起维护，并在麻省理工学院的许可下发布。