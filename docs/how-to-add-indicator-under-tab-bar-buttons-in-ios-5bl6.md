# 如何在 iOS 的标签栏按钮下添加指示器

> 原文：<https://dev.to/onmyway133/how-to-add-indicator-under-tab-bar-buttons-in-ios-5bl6>

## 选择指示标志

*   [https://developer . apple . com/documentation/ui kit/uitabbar/1623456-selectionindicatorimage](https://developer.apple.com/documentation/uikit/uitabbar/1623456-selectionindicatorimage)
*   应该设计足够高的图像，透明的背景和底部的指标

> 使用此属性指定自定义选择图像。您的图像呈现在选项卡栏的顶部，但在选项卡栏项目本身的内容之后。该属性的默认值为 nil，这将导致选项卡栏对选定的项目应用默认的突出显示

## 自定义可展开或可展开控制器

*   隐藏已有的`tabBar` `tabBar.isHidden = true`
*   定位自定义按钮

```
import UIKit

class CustomTabBarController: UITabBarController {
    private let bar = UIView()
    private var buttons = [UIButton]()

    override var viewControllers: [UIViewController]? {
        didSet {
            populate(viewControllers: viewControllers)
        }
    }

    override func viewDidLoad() {
        super.viewDidLoad()

        setup()
    }

    private func setup() {
        tabBar.isHidden = true
        bar.backgroundColor = R.color.background
        view.addSubview(bar)
        NSLayoutConstraint.on([
            bar.leftAnchor.constraint(equalTo: view.leftAnchor),
            bar.rightAnchor.constraint(equalTo: view.rightAnchor),
            bar.bottomAnchor.constraint(equalTo: view.bottomAnchor),
            bar.topAnchor.constraint(equalTo: tabBar.topAnchor)
        ])
    }

    private func populate(viewControllers: [UIViewController]) {
        buttons.forEach {
            $0.removeFromSuperview()
        }

        buttons = viewControllers.map({
            let button = UIButton()
            button.setImage($0.tabBarItem.image, for: .normal)
            bar.addSubview(button)
            return button
        })

        view.setNeedsLayout()
    }

    override func viewDidLayoutSubviews() {
        super.viewDidLayoutSubviews()

        let padding: CGFloat = 20
        let buttonSize = CGSize(width: 30, height: 44)
        let width = view.bounds.width - padding * 20

        for (index, button) in buttons.enumerated() {
            button.center = CGPoint(x: bar.center.x, y: bar.frame.height/2)
        }
    }
} 
```

## 处理 UITabBarControllerDelegate

*   超越`func tabBar(_ tabBar: UITabBar, didSelect item: UITabBarItem)`
*   `tabBar.subviews`包含 1 个私有`UITabBarBackground`和多个私有`UITabBarButton`

```
import UIKit

class CustomTabBarController: UITabBarController {
    let indicator: UIView = {
        let view = UIView()
        view.backgroundColor = R.color.primary
        view.frame.size = CGSize(width: 32, height: 2)
        view.layer.cornerRadius = 1

        return view
    }()

    override func viewDidLoad() {
        super.viewDidLoad()

        tabBar.addSubview(indicator)
        self.delegate = self
    }

    private func animate(index: Int) {
        let buttons = tabBar.subviews
            .filter({ String(describing: $0).contains("Button") })

        guard index < buttons.count else {
            return
        }

        let selectedButton = buttons[index]
        UIView.animate(
            withDuration: 0.25,
            delay: 0,
            options: .curveEaseInOut,
            animations: {
                let point = CGPoint(
                    x: selectedButton.center.x,
                    y: selectedButton.frame.maxY - 1
                )

                self.indicator.center = point
            },
            completion: nil
        )
    }

    override func viewDidLayoutSubviews() {
        super.viewDidLayoutSubviews()

        animate(index: selectedIndex)
    }
}

extension CustomTabBarController: UITabBarControllerDelegate {
    override func tabBar(_ tabBar: UITabBar, didSelect item: UITabBarItem) {
        guard
            let items = tabBar.items,
            let index = items.firstIndex(of: item)
        else {
            return
        }

        animate(index: index)
    }
} 
```

原帖[https://github.com/onmyway133/blog/issues/288](https://github.com/onmyway133/blog/issues/288)