# iOS 上的功能标志、A/B 测试和 MVT

> 原文：<https://dev.to/rwbutler/feature-flags-a-b-testing-mvt-on-ios-1nea>

# 在您的 iOS 应用中切换功能、阶段功能部署和运行 A/B 测试，而无需发布新版本的应用或为在线平台支付不必要的月费。

如今有很多平台收取月费，为 iOS 应用提供功能标记和 A/B 测试功能，但真正需要的只是一个 JSON 文件和一些客户端代码。

本文展示了如何使用开源的 [FeatureFlags](https://github.com/rwbutler/FeatureFlags) 框架捆绑或托管自己的 JSON 配置，为您的应用程序提供特性标记/测试功能，该框架在麻省理工学院许可下免费提供，并与 [Cocoapods](https://cocoapods.org/pods/FeatureFlags) 和 Carthage 兼容。

该框架的一些强大功能包括一些您可能没有想到的功能，例如向一定比例的用户推出 A/B 测试，远程调整该比例(可用于分阶段推出功能)，然后在您准备好这样做时向所有用户推出该功能。

## 安装

第一步是使用 Cocoapods 或 Carthage 安装 FeatureFlags pod / framework。如果你已经熟悉如何通过这些方法安装一个框架，你可能想跳到关于使用的部分。

### 可可布丁

CocoaPods 是一个依赖管理器，它将依赖集成到你的 Xcode 工作空间中。要使用 [RubyGems](https://rubygems.org/) 安装它，运行:

```
gem install cocoapods 
```

要使用 Cocoapods 安装 FeatureFlags，只需在 Podfile 中添加下面一行:

```
pod "FeatureFlags" 
```

然后运行命令:

```
pod install 
```

更多信息[见此处](https://cocoapods.org/#getstarted)。

### 袁绍

Carthage 是一个依赖管理器，它生成一个二进制文件，用于手工集成到您的项目中。它可以通过自制软件使用命令安装:

```
brew update
brew install carthage 
```

为了通过 Carthage 将 FeatureFlags 集成到您的项目中，在项目的 Cartfile 中添加下面一行:

```
github "rwbutler/FeatureFlags" 
```

从 macOS 终端运行`carthage update --platform iOS`构建框架，然后将`FeatureFlags.framework`拖到 Xcode 项目中。

更多信息[见此处](https://github.com/Carthage/Carthage#quick-start)。

## 用法

将框架集成到项目中后，下一步是使用 JSON 文件进行配置，该文件可以作为应用程序的一部分捆绑在一起，也可以远程安装。JSON 文件可能是新创建的，也可能是您已经在使用的现有配置 JSON 文件。只需在文件映射的顶层添加一个名为`features`的键到一个特性数组，如下所示:

```
{  "features":  []  } 
```

数组的内容取决于要配置的特性标志和测试。
让 FeatureFlags 知道在哪里可以找到你的配置文件:

```
guard let featuresURL = Bundle.main.url(forResource: "features", withExtension: "json") else { 
    return 
}
FeatureFlags.configurationURL = featuresURL 
```

或者:

```
guard let featuresURL = URL(string: "https://www.exampledomain.com/features.json") else {
    return 
}
FeatureFlags.configurationURL = featuresURL 
```

如果您选择远程托管您的 JSON 文件，您可以在您的应用程序捆绑包中提供一个捆绑的后备:

```
guard let fallbackURL = Bundle.main.url(forResource: "features", withExtension: "json") { 
    return 
}
FeatureFlags.localFallbackConfigurationURL = fallbackURL 
```

您的远程托管的 JSON 文件将始终优先于绑定的设置，并且远程定义的设置将被缓存，以便在用户离线的情况下，将应用从网络检索的最后设置。

## 特征标志

为了配置特性标志，在 JSON 配置中将特性对象添加到特性数组中。

```
{  "features":  [{  "name":  "Example Feature Flag",  "enabled":  false  }]  } 
```

然后在`Feature.Name`上添加一个扩展来导入您的特性标志，如下所示:

```
import FeatureFlags
extension Feature.Name {
    static let exampleFeatureFlag = Feature.Name(rawValue: "Example Feature Flag")
} 
```

确保原始值与 JSON 文件中的字符串匹配。然后调用下面的代码来检查特性标志是否启用:

```
Feature.isEnabled(.exampleFeatureFlag)) 
```

如果在您的`Feature.Name`扩展名中指定的字符串与 JSON 文件中的值不匹配，则返回的默认值是`false`。如果你需要检查特性是否存在，你可以写:

```
if let feature = Feature.named(.exampleFeatureFlag) {
    print("Feature name -> \(feature.name)")
    print("Feature enabled -> \(feature.isEnabled())")
} 
```

## A/B 测试

要配置 A/B 测试，将以下特性对象添加到 JSON 文件中的特性数组:

```
{  "name":  "Example A/B Test",  "enabled":  true,  "test-variations":  ["Group A",  "Group B"]  } 
```

特性标志和 A/B 测试之间的唯一区别是增加了一组测试变量。如果您向数组中添加两个测试变量，FeatureFlags 将假定您正在配置 A/B 测试-再添加任何变量，测试将自动变成多变量测试(MVT)。
使用`Feature.Name` :
上的扩展将您的特性导入代码

```
extension Feature.Name {
    static let exampleABTest = Feature.Name(rawValue: "Example A/B Test")
} 
```

然后使用以下命令来检查用户被分配到了哪个组:

```
if let test = ABTest(rawValue: .exampleABTest) {
    print("Is in group A? -> \(test.isGroupA())")
    print("Is in group B? -> \(test.isGroupB())")
} 
```

或者，您可能更喜欢下面的语法:

```
if let feature = Feature.named(.exampleABTest) {
    print("Feature name -> \(feature.name)")
    print("Is group A? -> \(feature.isTestVariation(.groupA))")
    print("Is group B? -> \(feature.isTestVariation(.groupB))")
    print("Test variation -> \(feature.testVariation())")
} 
```

## 特性 A/B 测试

特性 A/B 测试是 A/B 测试的细微变化(及其子类型)。在一般的 A/B 测试中，您可能想要检查用户是否被置于蓝色背景或红色背景测试变体中。特性 A/B 测试专门测试新特性的引入是否比没有该特性的控制组有所改进。因此，在特性 A/B 测试中，该特性要么关闭，要么打开。
要配置特性 A/B 测试，请使用以下 JSON:

```
{  "name":  "Example Feature A/B Test",  "enabled":  true,  //  whether  or  not  the  test  is  enabled  "test-variations":  ["Enabled",  "Disabled"]  } 
```

```
extension Feature.Name {
    static let exampleFeatureABTest = Feature.Name(rawValue: "Example Feature A/B Test")
} 
```

通过命名测试变体`Enabled`和`Disabled`，FeatureFlags 知道您的意图是建立一个特性 A/B 测试。

配置特性 A/B 测试比一般 A/B 测试有优势，因为不必编写:

```
if let feature = Feature.named(.exampleFeatureABTest) {
    print("Feature name -> \(feature.name)")
    print("Is group A? -> \(feature.isTestVariation(.enabled))")
    print("Is group B? -> \(feature.isTestVariation(.disabled))")
    print("Test variation -> \(feature.testVariation())")
} 
```

您可以简单地使用以下内容来确定用户被分配到哪个测试组:

```
Feature.isEnabled(.exampleFeatureABTest)) 
```

通常使用`Feature.enabled()`方法测试以查看一个特性是否被全局启用；在这个特定的实例中，如果用户属于接收新特性的组，它将返回`true`，如果用户属于控制组，它将返回`false`。注意，如果这个特性的 JSON 中的`enabled`属性被设置为`false`，这个方法也会返回`false`，也就是说，测试是全局禁用的。

## 多元(MVT)检验

多变量检验的配置遵循与 A/B 检验非常相似的模式。将下面的 feature 对象添加到 JSON 文件的 features 数组中:

```
{  "name":  "Example MVT Test",  "enabled":  true,  "test-variations":  ["Group A",  "Group B",  "Group C"]  } 
```

如果向阵列中添加两个以上的测试变体，FeatureFlags 知道您正在配置 MVT 测试。同样，使用`Feature.Name` :
上的扩展名将您的特性导入代码

```
extension Feature.Name {
    static let exampleMVTTest = Feature.Name(rawValue: "Example MVT Test")
} 
```

使用以下内容检查用户已被分配到哪个组:

```
if let feature = Feature.named(.exampleMVTTest) {
    print("Feature name -> \(feature.name)")
    print("Is group A? -> \(feature.isTestVariation(.groupA))")
    print("Is group B? -> \(feature.isTestVariation(.groupB))”)
    print("Is group C? -> \(feature.isTestVariation(.groupC))”)
    print("Test variation -> \(feature.testVariation())”)
} 
```

您可以随意命名您的测试变体:

```
{  "name":  "Example MVT Test",  "enabled":  true,  //  whether  or  not  the  test  is  enabled  "test-variations":  ["Red",  "Green",  "Blue"]  } 
```

只需在测试中创建一个扩展。在代码中映射测试变化的变化:

```
extension Test.Variation {
    static let red = Test.Variation(rawValue: "Red")
    static let green = Test.Variation(rawValue: "Green")
    static let blue = Test.Variation(rawValue: "Blue")
} 
```

然后检查用户被分配到哪个组:

```
if let feature = Feature.named(.exampleMVTTest) {
    print("Feature name -> \(feature.name)")
    print("Is red? -> \(feature.isTestVariation(.red))")
    print("Is green? -> \(feature.isTestVariation(.green))")
    print("Is blue? -> \(feature.isTestVariation(.blue))")
    print("Test variation -> \(feature.testVariation())")
} 
```

## 高级用法

### 测试偏差

默认情况下，对于任何 A/B 或 MVT 测试，用户同样可能被分配到每个指定的测试变量，即对于 A/B 测试，有 50%/50%的机会被分配到一个组或另一个组。对于有四个变量的 MVT 测试，被分配到每个变量的概率是 25%。
可以配置测试偏差，使分配给每个测试变量的可能性不相等。为此，只需将下面的 JSON 添加到您的特性对象中:

```
{  "features":  [{  "name":  "Example A/B Test",  "enabled":  true,  "test-biases":  [80,  20],  "test-variations":  ["Group A",  "Group B"]  }]  } 
```

在`test-biases`数组中指定的权重数必须等于测试变量数，并且必须等于 100，否则权重将被忽略，默认为相等的权重。

### 标签

如果您希望将相应的分析发送到用户被分配到的测试组，可以将标签附加到测试变化上。

为此，定义一个长度等于指定测试变量数的数组【T0:】T1

```
{  "features":  [{  "name":  "Example A/B Test",  "enabled":  true,  "test-biases":  [50,  50],  "test-variations":  ["Group A",  "Group B"],  "labels":  ["label1-for-analytics",  "label2-for-analytics"]  }]  } 
```

然后在代码中检索你的标签，你可以写如下:

```
if let feature = Feature.named(.exampleABTest) {
    print("Group A label -> \(feature.label(.groupA))")
    print("Group B label -> \(feature.label(.groupB))")
} 
```

### 滚出特性

FeatureFlags 框架最强大的特性是能够调整远程 JSON 配置文件中的测试偏差，并自动将用户重新分配到新的测试组。例如，您可能决定在第一周使用 10%/90%(即 10%的用户接收到新特性)的划分推出一个特性，在第二周使用 20%/80%的划分，依此类推。

只需更新`test-biases`数组中的权重，下次框架检查 JSON 配置时，就会重新分配组。

当你完成了对一个功能的 A/B 或 MVT 测试，你就已经收集了足够的分析来决定是否向你的整个用户群推广这个功能。此时，您可以通过将 JSON 文件中的`enabled`标志设置为`false`来决定完全禁用该特性，或者在测试成功的情况下，您可以通过从
调整 JSON 文件中的特性对象来决定向所有用户推出该特性

```
{  "features":  [{  "name":  "Example A/B Test",  "enabled":  true,  "test-biases":  [50,  50],  "test-variations":  ["Group A",  "Group B"],  "labels":  ["label1-for-analytics",  "label2-for-analytics"]  }]  } 
```

对所有用户全局启用的功能标志如下:

```
{  "features":  [{  "name":  "Example A/B Test",  "enabled":  true  }]  } 
```

### QA

为了测试你的新特性的两个变体是否正常工作，你可能需要在运行时调整你的特性标志/测试的状态。为此，FeatureFlags 提供了`FeatureFlagsViewController`，允许您在应用程序的调试版本或循环 A/B 测试或 MVT 测试版本中打开/关闭特性标志。

要显示视图控制器，指定所需的导航首选项，然后通过提供`UINavigationController` :
来按下视图控制器

```
let navigationSettings = FeatureFlagsViewControllerNavigationSettings(
    autoClose: true, 
    closeButtonAlignment: .right, 
    closeButton: .save, 
    isNavigationBarHidden: false
)

FeatureFlags.pushFeatureFlags(
    delegate: self, 
    navigationController: navigationController, 
    navigationSettings: navigationSettings
) 
```

[![Feature Flags List](img/de36cafd4afc8651b39f7cf72e3f4e7b.png "Feature Flags List")](https://res.cloudinary.com/practicaldev/image/fetch/s--SlLN-wc_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/rwbutler/FeatureFlags/raw/master/doimg/feature-flags-view-controller.png)

如果您需要每个功能标志/测试状态的更多信息，您可以使用 3D Touch 查看/弹出更多信息。

[![Feature Details](img/f6cb40bd9adb390d05b84fe2e9e67281.png "Feature Details")](https://res.cloudinary.com/practicaldev/image/fetch/s--jPnTdJbB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/rwbutler/FeatureFlags/raw/master/doimg/feature-details-view-controller.png)

### 刷新配置

如果您需要在任何时候刷新您的配置，您可以调用`FeatureFlags.refresh()`,它可以选择接受一个完成闭包，以便在刷新完成时通知您。

如果您已经选择将您的功能标志信息作为您的应用程序已经获取的现有 JSON 文件的一部分，您可能希望使用以下方法传递 JSON 文件数据，以避免重复的网络调用:

```
FeatureFlags.refreshWithData(_:completion:) 
```

## 总结

FeatureFlags 提供了一个廉价的解决方案来实现功能标记、A/B 测试和 MVT，使您能够自行管理您的配置，并整合强大的功能，如分阶段推出功能，您可以通过调整配置文件中的测试偏差来轻松控制这些功能。我们已经看到，通过将 A/B 测试或 MVT 转换为全球启用的功能标志，甚至可以向所有用户推出功能，这样我们就不必向商店发布新版本的应用程序。

在麻省理工学院的许可下，可以在 [GitHub](https://github.com/rwbutler/FeatureFlags) 上找到开源的 FeatureFlags，它与 [Cocoapods](https://cocoapods.org/pods/FeatureFlags) 和 Carthage 都兼容。

* * *

[![FeatureFlags Logo](img/fade7893ab92d554217fa88e440806af.png "FeatureFlags Logo")](https://res.cloudinary.com/practicaldev/image/fetch/s--SDVZKOtu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/rwbutler/FeatureFlags/raw/master/doimg/feature-flags-logo.png)