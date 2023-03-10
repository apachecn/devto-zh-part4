# 在 UIKit 中测试动画

> 原文：<https://dev.to/elpassion/testing-animations-in-uikit-1fin>

我们在 [EL Passion](https://www.elpassion.com) 认真对待测试。在实现 iOS 应用程序时，我们总是试图实践 TDD、结对编程和定期代码审查。我们希望我们的软件稳定并且没有错误。我们经常开发设计出色的应用程序，包括动画。在这种情况下，我们引入了基于快照的测试，它不仅适用于静态 UI，也适用于动画。

## 举例

下面你可以看到一个真实的例子，摘自[电子商务今日交易互动，iOS 演示](https://github.com/elpassion/ecommerce-ios-demo)。我们为视图控制器转换实现了自定义动画，并使用快照测试对其进行了测试。

| 应用 | 测试快照 |
| --- | --- |
| ![Ecommerce app](img/987a9a688dddf192928c646adb1f61cb.png) | ![Test snapshots](img/798f293dde2f0221c9ba5e1de0de76b6.png) |

## 动画

看一个简化的例子。假设我们有一个动画按钮:

[![Button animation](img/1133d61fb14a65cc64d52b57bfcd2097.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5IKxPsOZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://github.com/elpassion/Showcase/raw/master/content/testing-UIKit-animations/button_animation.gif)

它是使用本机`UIKit`关键帧动画 API:
实现的

```
func animate() {
    UIView.animateKeyframes(
        withDuration: 2,
        delay: 0,
        options: [],
        animations: {
            UIView.addKeyframe(
                withRelativeStartTime: 0,
                relativeDuration: 0.5,
                animations: {
                    self.button.transform = CGAffineTransform(scaleX: 1.5, y: 1.5)
                    self.button.backgroundColor = .red
                }
            )
            UIView.addKeyframe(
                withRelativeStartTime: 0.5,
                relativeDuration: 0.5,
                animations: {
                    self.button.transform = .identity
                    self.button.backgroundColor = .blue
                }
            )
        }
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以在[exampleviewcontroller . swift](https://github.com/elpassion/Showcase/blob/master/content/testing-UIKit-animations/ExampleViewController.swift)文件中找到完整的源代码。

## 测试

为了测试我们的动画按钮，我们将使用来自[免点](https://www.pointfree.co)的[快照测试](https://github.com/pointfreeco/swift-snapshot-testing)库。

在我们的`XCTestCase`中，我们必须将动画视图添加到一个窗口:

```
override func setUp() {
    sut = ExampleViewController()
    window = UIWindow(frame: CGRect(x: 0, y: 0, width: 220, height: 100))
    window.rootViewController = sut
    window.isHidden = false
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以使用[uiviewpropertyimator](https://developer.apple.com/documentation/uikit/uiviewpropertyanimator)来控制动画进度。在设置好动画的进度后，我们可以制作一个包含窗口的快照:

```
func testAnimation() {
    let animator = UIViewPropertyAnimator(duration: 1, curve: .linear, animations: {
        self.sut.animate()
    })

    (0...10).map { CGFloat($0) / 10.0 }.forEach { animationProgress in
        animator.fractionComplete = animationProgress
        assertSnapshot(
            matching: window,
            as: .image(drawHierarchyInKeyWindow: true),
            named: "animation_\(String(format: "%02.0f", animationProgress * 10))"
        )
    }

    animator.pauseAnimation()
    animator.stopAnimation(false)
    animator.finishAnimation(at: .current)
} 
```

Enter fullscreen mode Exit fullscreen mode

这将导致生成 11 个快照图像，我们的动画将在每次运行测试时与之进行比较:

[![Button animation snapshots](img/fe0a72bd8c742857c22a6059a6e23515.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QfPyFkI4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/elpassion/Showcase/raw/master/content/testing-UIKit-animations/button_snapshots.png)

你可以在[exampleviewcontrollertests . swift](https://github.com/elpassion/Showcase/blob/master/content/testing-UIKit-animations/ExampleViewControllerTests.swift)文件中找到给出的`XCTestCase`子类的完整源代码。

## 执照

版权所有 2019 [EL Passion](https://www.elpassion.com)