# 不同的数据源

> 原文：<https://dev.to/gualtierofr/diffable-data-sources-4fb7>

原帖[http://www.gfrigerio.com/diffable-data-sources/](http://www.gfrigerio.com/diffable-data-sources/)

WWDC 2019 真的挤满了 iOS 的新东西，TableViews 和 CollectionViews 的新数据源没有像 SwiftUI 一样受到关注，但对于仍然使用 UIKit 的人来说，这是一个非常好的补充。
我将把我的帖子集中在 UITableViewDiffableDataSource 上，但有一个类似的 API 用于集合视图，如果你想了解更多，你可以在这个[链接](https://developer.apple.com/videos/play/wwdc2019/220/)上观看一个很棒的会议。
像往常一样，你可以在 [GitHub](https://github.com/gualtierofrigerio/DiffableDataSource) 上找到一个示例项目中的所有代码

## 古老的方式

在 iOS 13 之前，有两种方法可以用新数据更新表格视图。第一种，不是真正有效的一种，是调用 reloadData 并重新加载整个 TableView。第二个是调用 beginUpdates 和 endUpdates，并在这些调用之间放置操作，或者在 iOS 11 中引入 API performBatchUpdates。
该 API 的问题是以正确的顺序执行所有操作，并且底层数据同时发生变化。

## 新的 API

幸运的是，苹果在 iOS 13 中发布了一组新的 API 来处理这种问题。我们不再需要重新加载整个表或以正确的顺序执行批量更新，现在有一个系统 API 来做同样的事情，但没有必要指定对数据集执行的操作，API 将为我们预测它。通过定义快照可以对一组数据执行更新，底层 API 将确定差异并对表视图执行操作。

我认为一个很好的例子是对数据列表进行搜索。在执行搜索时，让单元格显示动画是很棒的，这样匹配搜索的单元格仍然可见，而其他单元格则从列表中消失。我正在使用一个公共 API 和一个啤酒列表，我有一个包含名称、描述等的结构啤酒。我想在桌子上展示。对于这个例子，我使用了 UITableViewController 的一个子类，但是你看不到 UITableViewDataSource 的任何函数，只能看到来自委托的函数。通过创建 UITableViewDiffableDataSource 的实例来替换数据源。你可以在这里看到代码

```
private func configureDataSource() {
    tableDataSource = UITableViewDiffableDataSource(tableView: self.tableView, cellProvider: { (tableView, indexPath, beer) -> UITableViewCell? in
        let cell = tableView.dequeueReusableCell(withIdentifier: self.cellIdentifier)
        cell?.textLabel?.text = beer.name
        cell?.detailTextLabel?.text = beer.description
        return cell
    })
} 
```

如您所见，我们可以在通过闭包创建数据源时直接填充表格视图单元格，这非常方便。如您所见，我们必须指定两种类型:第一种用于部分，第二种用于单元格。它们需要是可散列的，所以我为该部分使用了一个枚举，并使 Beer 结构符合可散列的。注意，闭包为我们提供了表视图、索引路径和啤酒记录。我们不必用 indexPath.row 在啤酒数组中寻找啤酒，它已经存在了。到目前为止一切顺利，但是我们如何填充 TableView 呢？我们必须像我之前提到的那样创建快照

```
private func updateDataSource(withBeers beers:[Beer], animated:Bool) {
    var snapshot = NSDiffableDataSourceSnapshot()
    snapshot.appendSections([.main])
    snapshot.appendItems(beers, toSection: .main)
    tableDataSource.apply(snapshot, animatingDifferences: animated)
} 
```

就像 uitableviewdiffabledata source nsdiffabledata sources napshot 需要初始化两种符合 Hashable 的类型。我们如何创建一个部分？通过将节类型附加到快照，下面一行将项目放入特定的节中。就这样，不再需要调用数据源方法来获取节的数量和一个节中的项数。完成快照后，我们可以将其应用到数据源。有一个参数可以使变化动画化，在启动时我将它设置为 false，而在搜索时我传递 true，这样我们可以在输入时看到一个漂亮的动画。

我们看到了数据源，那么代表呢？没有新的 API，但是正如你所看到的，我们没有使用 IndexPaths，那么我们如何获得选中的项目呢？新的数据源为我们解决了这个问题

```
override func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
    if let beer = tableDataSource.itemIdentifier(for: indexPath) {
        print("selected beer \(beer)")
    }
} 
```

itemIdentifier(for:)允许我们获取与所选索引路径关联的项。我们讨论的是在当前快照中找到的条目，所以即使 Beer 的数组随着时间的推移而改变，只要我们通过快照更新表视图，我们就不会有问题，如果需要，数据源对象可以随时检索快照。还有一个从元素中获取 IndexPath 的函数。

这只是对新的 diffable 数据源的一个快速介绍，我没有涉及 UICollectionView 的 API，但它们与您在本例中看到的非常相似。编码快乐！