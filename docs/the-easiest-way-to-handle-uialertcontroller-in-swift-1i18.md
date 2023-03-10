# 在 Swift 中处理 UIAlertController 的最简单方法

> 原文：<https://dev.to/ngo275/the-easiest-way-to-handle-uialertcontroller-in-swift-1i18>

UIKit 提供了 UIAlertController，但是有时候用起来很烦。你必须得到顶级的 UIViewController。我将介绍一种更简单的方法来处理 UIAlertController。

```
// AlertController.swift

UIKit 

class AlertController: UIAlertController {
    private lazy var alertWindow: UIWindow = {
        let window = UIWindow(frame: UIScreen.main.bounds)
        window.rootViewController = ClearViewController()
        window.backgroundColor = UIColor.clear
        window.windowLevel = UIWindow.Level.alert
        return window
    }()

    /// Shows a custom alert controller.
    func show(animated flag: Bool = true, completion: (() -> Void)? = nil) {
        guard let rootVC = alertWindow.rootViewController else { return }
        alertWindow.makeKeyAndVisible()
        rootVC.present(self, animated: flag, completion: completion)
    }
}

private class ClearViewController: UIViewController {
    override var preferredStatusBarStyle: UIStatusBarStyle {
        return UIApplication.shared.statusBarStyle
    }

    override var prefersStatusBarHidden: Bool {
        return UIApplication.shared.isStatusBarHidden
    }
} 
```

可以像下面这样使用`AlertController`。此警报将允许您从任何地方轻松显示警报视图。我的意思是，你再也不用从 UIViewController 调用`present(viewControllerToPresent: UIViewController>, animated: Bool, completion: (() -> Void)?)`了。

```
func displayAlert() {
    let alert = AlertController(title: "Error", message: "Something happened", preferredStyle: .alert)
    let action = UIAlertAction(title: "OK", style: .default)
    alert.addAction(action)
    alert.show()
} 
```

你可能想像下面这样扩展`Error`。可以显示类似`error.displayAlert()`的错误提示。很简单，不是吗？

```
extension Error {
    func displayAlert(completion: (() -> Void)? = nil) {
        let alert = AlertController(title: "Error", message: localizedDescription, preferredStyle: .alert)
        let action = UIAlertAction(title: "OK", style: .default)
        alert.addAction(action)
        alert.show(completion: completion)
    }
} 
```