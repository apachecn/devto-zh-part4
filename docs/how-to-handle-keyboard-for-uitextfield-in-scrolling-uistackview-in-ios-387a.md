# 如何在 iOS 的滚动 UIStackView 中处理 UITextField 的键盘

> 原文：<https://dev.to/onmyway133/how-to-handle-keyboard-for-uitextfield-in-scrolling-uistackview-in-ios-387a>

首先，为了使 UIStackView 可滚动，将其嵌入 UIScrollView 中。阅读[如何在 iOS 中的 UIScrollView 内嵌入 UIStackView】](https://github.com/onmyway133/blog/issues/324)

最好听听`keyboardWillChangeFrameNotification`，因为它包含不同情况下键盘的框架变化，如自定义键盘、语言。

> 就在键盘框架改变之前发布。

```
class KeyboardHandler {
    let scrollView: UIScrollView
    let stackView: UIStackView
    var observer: AnyObject?
    var keyboardHeightConstraint: NSLayoutConstraint!

    struct Info {
        let frame: CGRect
        let duration: Double
        let animationOptions: UIView.AnimationOptions
    }

    init(scrollView: UIScrollView, stackView: UIStackView) {
        self.scrollView = scrollView
        self.stackView = stackView
    }
} 
```

为了让 scrollView 滚动到它的`contentSize`之外，我们可以改变它的`contentInset.bottom`。另一种方法是给`UIStackView`添加一定高度的虚拟视图，并修改其`NSLayoutConstraint` `constant`

我们不能在 init 里面访问`self`，所以最好有 setup 函数

```
func setup() {
    let space = UIView()
    keyboardHeightConstraint = space.heightAnchor.constraint(equalToConstant: 0)
    NSLayoutConstraint.on([keyboardHeightConstraint])
    stackView.addArrangedSubview(spa
    observer = NotificationCenter.default.addObserver(
        forName: UIResponder.keyboardWillChangeFrameNotification,
        object: nil,
        queue: .main,
        using: { [weak self] notification in
            self?.handle(notification)
        }
    )
} 
```

将`Notification`转换成一个方便的`Info`结构

```
func convert(notification: Notification) -> Info? {
    guard
        let frameValue = notification.userInfo?[UIResponder.keyboardFrameEndUserInfoKey] NSValue,
        let durationotification.userInfo?[UIResponder.keyboardAnimationDurationUserInfoKey] as? NSNumber,
        let raw = notification.userInfo?[UIResponder.keyboardAnimationCurveUserInfoKey] NSNumber
    else {
        return nil

    return Info(
        frame: frameValue.cgRectValue,
        duration: duration.doubleValue,
        animationOptions: UIView.AnimationOptions(rawValue: raw.uintValue)
    )
} 
```

然后我们可以与 UIScreen 进行比较，检查键盘是否显示或隐藏

```
func handle(_ notification: Notification) {
    guard let info = convert(notification: notification) else {
        return

    let isHiding = info.frame.origin.y == UIScreen.main.bounds.height
    keyboardHeightConstraint.constant = isHiding ? 0 : info.frame.hei
    UIView.animate(
        withDuration: info.duration,
        delay: 0,
        options: info.animationOptions,
        animations: {
            self.scrollView.layoutIfNeeded()
            self.moveTextFieldIfNeeded(info: info)
    }, completion: nil)
} 
```

要移动`UITextField`，我们可以使用 [`scrollRectToVisible(_:animated:)`](https://developer.apple.com/documentation/uikit/uiscrollview/1619439-scrollrecttovisible) ，但是我们无法控制想要滚动多少

> 该方法滚动内容视图，以便 rect 定义的区域在滚动视图中可见。如果该区域已经可见，则该方法不执行任何操作。

另一种方法是检查键盘是否重叠`UITextField`。为此，我们使用 [`convertRect:toView:`](https://developer.apple.com/documentation/uikit/uiview/1622504-convertrect) 和`nil`目标，因此它使用窗口坐标。由于键盘框架总是相对于窗口，我们有相同的坐标空间的框架。

> 将矩形从接收控件的坐标系转换到另一个视图的坐标系。
> 
> rect:在接收控件的本地坐标系(边界)中指定的矩形。
> 视图:作为转换操作目标的视图。如果 view 为 nil，此方法将转换为窗口基坐标。否则，视图和接收器必须属于同一个 UIWindow 对象。

```
func moveTextFieldIfNeeded(info: Info) {
    guard let input = stackView.arrangedSubviews
        .compactMap({ $0 as? UITextField })
        .first(where: { $0.isFirstResponder })
    else {
        return

    let inputFrame = input.convert(input.bounds, to: nil)
    if inputFrame.intersects(info.frame) {
        scrollView.setContentOffset(CGPoint(x: 0, y: inputFrame.height), animated: true)
    } else {
        scrollView.setContentOffset(.zero, animated: true)
    }
} 
```

* * *

原帖[https://github.com/onmyway133/blog/issues/329](https://github.com/onmyway133/blog/issues/329)