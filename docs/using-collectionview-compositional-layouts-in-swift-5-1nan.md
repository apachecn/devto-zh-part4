# 在 Swift 5 中使用集合查看组合布局

> 原文：<https://dev.to/kevinmaarek/using-collectionview-compositional-layouts-in-swift-5-1nan>

在 2019 年 WWDC 上，苹果推出了一种新形式的 UICollectionViewLayout。如果你曾经使用过 UICollectionView，你可能会为你的布局属性或者调整 FlowLayout 而苦恼。我个人实际上花了几个小时试图得到一个好结果，为所有 iOS 版本工作，在每个设备上，每个大小级别，每个方向。

嗯，认识一下`UICollectionViewCompositionalLayout`！

一个布局，允许您以一种非常简单的方式指定您的行/项、组和节的大小和插入。使用复合布局，你可以在几行代码中构建非常复杂的布局。
我们来看看吧。

要开始使用 CollectionViewLayout，让我们快速创建一个 collectionViewCell。
这是一个简单的 UICollectionViewCell，它有一个带阴影和圆角的容器，里面有一个圆形的彩色视图:

```
class Cell: UICollectionViewCell {
    var container: UIView = {
        let view = UIView()
        view.backgroundColor = UIColor.white
        view.layer.shadowColor = UIColor.black.cgColor
        view.layer.cornerRadius = 4
        view.layer.shadowOpacity = 0.5
        view.layer.shadowRadius = 4
        view.layer.shadowOffset = CGSize(width: 0, height: 2)
        view.translatesAutoresizingMaskIntoConstraints = false
        return view
    }()

    var colorView: UIView = {
        let view = UIView()
        view.backgroundColor = UIColor.red
        view.layer.cornerRadius = 10
        view.translatesAutoresizingMaskIntoConstraints = false
        return view
    }()

    override init(frame: CGRect) {
        super.init(frame: frame)
        self.contentView.addSubview(self.container)
        self.container.topAnchor.constraint(equalTo: self.contentView.topAnchor).isActive = true
        self.container.leftAnchor.constraint(equalTo: self.contentView.leftAnchor).isActive = true
        self.container.bottomAnchor.constraint(equalTo: self.contentView.bottomAnchor).isActive = true
        self.container.rightAnchor.constraint(equalTo: self.contentView.rightAnchor).isActive = true

        self.container.addSubview(self.colorView)
        self.colorView.centerXAnchor.constraint(equalTo: self.container.centerXAnchor).isActive = true
        self.colorView.centerYAnchor.constraint(equalTo: self.container.centerYAnchor).isActive = true
        self.colorView.widthAnchor.constraint(equalToConstant: 20).isActive = true
        self.colorView.heightAnchor.constraint(equalToConstant: 20).isActive = true
    }

    required init?(coder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

看起来像:

[![](img/8ac1208c72d419c56c2f0a7d090167b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BcbU7_ZW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tmc12evhendfiwyktlwp.png)

现在，我们将创建一个内部包含 UICollectionView 的快速 view controller:

```
class ViewController: UIViewController, UICollectionViewDelegate, UICollectionViewDataSource {
    var collectionView: UICollectionView!
    let colors = [UIColor.red, UIColor.blue, UIColor.green, UIColor.orange, UIColor.purple]

    override func viewDidLoad() {
        super.viewDidLoad()
        // init collection view
        self.collectionView = UICollectionView()
        self.collectionView.backgroundColor = UIColor.white
        // set the delegate and dataSource on self
        self.collectionView.delegate = self
        self.collectionView.dataSource = self
        // register our cell
        self.collectionView.register(Cell.self, forCellWithReuseIdentifier: "cell")

        // place the collectionView in the viewController's view
        self.collectionView.translatesAutoresizingMaskIntoConstraints = false
        self.view.addSubview(self.collectionView)
        NSLayoutConstraint.activate([
            self.collectionView.topAnchor.constraint(equalTo: self.view.layoutMarginsGuide.topAnchor),
            self.collectionView.bottomAnchor.constraint(equalTo: self.view.bottomAnchor),
            self.collectionView.leftAnchor.constraint(equalTo: self.view.leftAnchor),
            self.collectionView.rightAnchor.constraint(equalTo: self.view.rightAnchor)
            ])
    }

    // data source

    func numberOfSections(in collectionView: UICollectionView) -> Int {
        return 3
    }

    func collectionView(_ collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int {
        return 5
    }

    func collectionView(_ collectionView: UICollectionView, cellForItemAt indexPath: IndexPath) -> UICollectionViewCell {
        if let cell = collectionView.dequeueReusableCell(withReuseIdentifier: "cell", for: indexPath) as? Cell {
            cell.colorView.backgroundColor = colors[indexPath.row]
            return cell
        } else {
            return UICollectionViewCell()
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们需要正确初始化 collectionView，包括边界和布局。让我们创建一个函数，我们将调用`makeLayout()`，它将返回我们的组合布局。

UICollectionViewCompositionalLayout 用一个块初始化，该块接受两个参数:section 和环境，并返回 NSCollectionLayoutSection。正如您所猜测的，在呈现每个不同的部分时都会调用它。

```
 func makeLayout() -> UICollectionViewLayout {
        let layout = UICollectionViewCompositionalLayout { (section: Int, environment: NSCollectionLayoutEnvironment) -> NSCollectionLayoutSection? in
            let item = NSCollectionLayoutItem(layoutSize: NSCollectionLayoutSize(widthDimension: NSCollectionLayoutDimension.fractionalWidth(1.0), heightDimension: NSCollectionLayoutDimension.absolute(44)))
            item.contentInsets = NSDirectionalEdgeInsets(top: 5, leading: 5, bottom: 5, trailing: 5)
            let groupSize = NSCollectionLayoutSize(widthDimension: .fractionalWidth(1.0),  heightDimension: .absolute(50))
            let group = NSCollectionLayoutGroup.horizontal(layoutSize: groupSize, subitem: item, count: 2)
            let section = NSCollectionLayoutSection(group: group)
            section.contentInsets = NSDirectionalEdgeInsets(top: 20, leading: 20, bottom: 20, trailing: 20)
            return section
        }
        return layout
    } 
```

Enter fullscreen mode Exit fullscreen mode

在这几行中，我们设置了几个值得注意的对象:

*   Item : `NSCollectionLayoutItem`用它的大小(width&height)初始化。在这个代码示例中，我还设置了 contentInsets。这基本上是一个细胞。
*   Group : `NSCollectionLayoutGroup`以其大小、项目和数量开始。一个组代表布局的一条“线”。计数是一行中的项目数。如果你愿意，可以称之为专栏。
*   段:`NSCollectionLayoutSection`用其组初始化。这是一个部门。

[![](img/cd77a7fbc445b291f5be4121436aae18.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lWrHkJAE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/51wrsv9uyq3k7try37ne.png)

因此，我们可以通过分组播放物品计数来制作一个有趣的布局，如下所示:

[![](img/ad0cf6df315d932ef8f5e98793bb799c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PxBw6-T6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y0d4wa5d6v42g67wasc8.png)

轻松点。我们将创建一个`func numberOfColumns(section: Int) -> Int`，并在`NSCollectionLayoutGroup` init 中调用它。

```
 func numberOfColumns(section: Int) -> Int {
        switch section {
        case 0:
            return 5
        case 1:
            return 3
        default:
            return 1
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

组合布局也允许正交滚动，你知道，就像在 AppStore 中，双向滚动。但是我们下次再吃。

希望你喜欢这个小介绍。如果想在构图布局上更深入，我强烈推荐 [WWDC 2019 Session 215](https://www.swiftjectivec.com/ios-13-notable-uikit-additions/) 。

编码快乐！:)