# UIScrollView 中的自定义页面

> 原文：<https://dev.to/elpassion/custom-pagination-in-uiscrollview-c45>

在`UIScrollView`(或`UITableView`或`UICollectionView`)中实现自定义分页有多种方式。下面你可以找到非常简单有效的一个。

## 举例

下面你可以看到真实的例子，摘自[电子商务今日交易互动，iOS 演示](https://github.com/elpassion/ecommerce-ios-demo)。

| 例子 |
| --- |
| ![Preview](img/382712de522e9f58c5d80cab3f08bcb9.png) |

## 实现

我们将使用`ScrollPageController`类来控制`UIScrollView`分页和更新`UIPageControl` :

```
struct ScrollPageController {

    /// Computes page offset from page offsets array for given scroll offset and velocity
    ///
    /// - Parameters:
    ///   - offset: current scroll offset
    ///   - velocity: current scroll velocity
    ///   - pageOffsets: page offsets array
    /// - Returns: target page offset from array or nil if no page offets provided
    func pageOffset(for offset: CGFloat, velocity: CGFloat, in pageOffsets: [CGFloat]) -> CGFloat? {
        let pages = pageOffsets.enumerated().reduce([Int: CGFloat]()) {
            var dict = $0
            dict[$1.0] = $1.1
            return dict
        }
        guard let page = pages.min(by: { abs($0.1 - offset) < abs($1.1 - offset) }) else {
            return nil
        }
        if abs(velocity) < 0.2 {
            return page.value
        }
        if velocity < 0 {
            return pages[pageOffsets.index(before: page.key)] ?? page.value
        }
        return pages[pageOffsets.index(after: page.key)] ?? page.value
    }

    /// Cumputes page fraction from page offsets array for given scroll offset
    ///
    /// - Parameters:
    ///   - offset: current scroll offset
    ///   - pageOffsets: page offsets array
    /// - Returns: current page fraction in range from 0 to number of pages or nil if no page offets provided
    func pageFraction(for offset: CGFloat, in pageOffsets: [CGFloat]) -> CGFloat? {
        let pages = pageOffsets.sorted().enumerated()
        if let index = pages.first(where: { $0.1 == offset })?.0 {
            return CGFloat(index)
        }
        guard let nextOffset = pages.first(where: { $0.1 >= offset })?.1 else {
            return pages.map { $0.0 }.last.map { CGFloat($0) }
        }
        guard let (prevIdx, prevOffset) = pages.reversed().first(where: { $0.1 <= offset }) else {
            return pages.map { $0.0 }.first.map { CGFloat($0) }
        }
        return CGFloat(prevIdx) + (offset - prevOffset) / (nextOffset - prevOffset)
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

如果您正在为您的项目寻找嵌入式解决方案，请查看附件[scrollpagecontroller . swift](https://github.com/elpassion/Showcase/blob/master/content/UIScrollView-custom-pagination/ScrollPageController.swift)文件及其在[scrollpagecontrollerspec . swift](https://github.com/elpassion/Showcase/blob/master/content/UIScrollView-custom-pagination/ScrollPageControllerSpec.swift)中的测试。

## 如何使用

很简单:

```
class ExampleViewController: UIViewController, UIScrollViewDelegate {

    // to make pagination working, implement this delegate function:

    func scrollViewWillEndDragging(_ scrollView: UIScrollView, withVelocity velocity: CGPoint, targetContentOffset: UnsafeMutablePointer<CGPoint>) {
        if let pageOffset = ScrollPageController().pageOffset(
            for: scrollView.contentOffset.x, 
            velocity: velocity.x, 
            in: pageOffsets(in: scrollView)
        ) {
            targetContentOffset.pointee.x = pageOffset
        }
    }

    // if you have a page control you would like to update when scrolling:

    func scrollViewDidScroll(_ scrollView: UIScrollView) {
        if let pageFraction = ScrollPageController().pageFraction(
            for: scrollView.contentOffset.x, 
            in: pageOffsets(in: scrollView)
        ) {
            let pageControl: UIPageControl = // your page controll
            pageControl.currentPage = Int(round(pageFraction))
        }
    }

    private func pageOffsets(in scrollView: UIScrollView) -> [CGFloat] {
        // return page offsets, see bellow for an example
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

如果要逐页滚动查看边框宽度:

```
 private func pageOffsets(in scrollView: UIScrollView) -> [CGFloat] {
        let pageWidth = scrollView.bounds.width
                        - scrollView.adjustedContentInset.left
                        - scrollView.adjustedContentInset.right
        let numberOfPages = Int(ceil(scrollView.contentSize.width / pageWidth))
        return (0..<numberOfPages).map { CGFloat($0) * pageWidth - scrollView.adjustedContentInset.left }
    } 
```

Enter fullscreen mode Exit fullscreen mode

如果要逐页滚动查看子视图:

```
 private func pageOffsets(in scrollView: UIScrollView) -> [CGFloat] {
        return scrollView.subviews
                         .compactMap { $0 as? PageView }
                         .map { $0.frame.minX - scrollView.adjustedContentInset.left }
    } 
```

Enter fullscreen mode Exit fullscreen mode

如果你喜欢现实生活中的例子，请查看[电子商务今日交易互动，iOS 演示](https://github.com/elpassion/ecommerce-ios-demo)实现。

## 执照

版权所有 2019 [EL Passion](https://www.elpassion.com)