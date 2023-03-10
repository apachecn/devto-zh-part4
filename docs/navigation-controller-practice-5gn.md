# 导航控制器实践

> 原文：<https://dev.to/casualty/navigation-controller-practice-5gn>

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [托马斯迪](https://github.com/thomasdye) / [彩虹](https://github.com/thomasdye/Rainbow)

<article class="markdown-body entry-content container-lg" itemprop="text">

# 彩虹

## 这个项目旨在帮助更好地理解导航控制器

请随意克隆并亲自检查。这是学习如何制作多页面应用程序的良好基础。

</article>

[View on GitHub](https://github.com/thomasdye/Rainbow)

在这里，我们将使用[MyColor]作为类型来创建颜色数组，并将 numberOfRowsInSection 作为颜色返回。count 是指我们将存储在 var colors 中的每种颜色)

```
import UIKit

class ColorsTableViewController: UITableViewController {

    var colors: [MyColor] = [MyColor(name: "Red", color: .red),
                             MyColor(name: "Orange", color: .orange),
                             MyColor(name: "Yellow", color: .yellow),
                             MyColor(name: "Green", color: .green),
                             MyColor(name: "Blue", color: .blue),
                             MyColor(name: "Cyan", color: .cyan),
                             MyColor(name: "Purple", color: .purple)]

    override func viewDidLoad() {
        super.viewDidLoad()

    }

    // MARK: - Table view data source

    override func numberOfSections(in tableView: UITableView) -> Int {
        // #warning Incomplete implementation, return the number of sections

        return 1
    }

    override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        // #warning Incomplete implementation, return the number of rows
        return colors.count
    } 
```

在 cellForRowAt 下，我们希望用与我们的项目相关的独特的东西来替换“ReuseIdentifier”。对于这个实例，我们将使用“ColorCell ”,但这给我们留下了一个问题。我们需要将新的标识符设置为与 ColorCell 相同的值。这是通过选择 Main.storyboard 文件上的原型单元格，选择“属性检查器”并将标识符设置为 ColorCell 来实现的。

```
 override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "ColorCell", for: indexPath)

        let color = colors[indexPath.row]

        cell.textLabel?.text = color.name

        return cell
    }

    // MARK: - Navigation

    override func prepare(for segue: UIStoryboardSegue, sender: Any?) {

        if segue.identifier == "ShowColorSegue" {

            guard let indexPath = tableView.indexPathForSelectedRow,
                let colorDetailVC = segue.destination as? ColorDetailViewController else { return }

            let cellColor = colors[indexPath.row]

            colorDetailVC.cellColor = cellColor

        }
    }
} 
```