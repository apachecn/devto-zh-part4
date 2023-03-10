# R 语言:如何在 R 中将数据导出到 CSV

> 原文：<https://dev.to/maheshkay/r-language-how-to-export-data-to-csv-in-r-2g6p>

让我们学习如何将数据导出到 r。当我们获取数据时，我们打算处理它，然后为用户导出它。在这里，用户可以使用它来可视化决策。并且在一些其他情况下，用户可以使用该输出来通读其他软件。

因此，在这种情况下，我们将看一看一个用例，我们基本上将数据帧导出为 CSV 文件。

可以看看教程的视频说明——[如何在 R](https://www.youtube.com/watch?v=H-sx_axnltA) 中将数据导出为 CSV 文件。

[https://www.youtube.com/embed/H-sx_axnltA](https://www.youtube.com/embed/H-sx_axnltA)

在导出数据之前，我们先创建一个数据框。

```
 df <- data.frame("No" = 1:2, "Age" = c(21,31), "Name" = c("Abbey","Bob")) 
```

这里我们用 df 作为我们的数据帧变量名。我们有这两个人的数据，我们有这些人的号码，年龄和名字。如果你愿意，你可以添加更多，但是为了简单起见，我只限于两个。您也可以利用外部数据源。

现在让我们将这些数据导出到 CSV。

```
write.csv(df,'D\csvData.csv', row.names = FALSE) 
```

这里我们利用的是 ***write.csv()*** 的方法。这是内置在 R 语言中的。所以我们没有使用任何外部库来完成这个任务。我们还传递存储文件的路径。

假设你有一个 D: drive，你希望这个名为 ***csvdata.csv*** 的文件存储在那里。最后 **row.names = FALSE** 这样设置是因为我们已经通过 dataframe 传递了特定于行的数据，所以不需要指定它们。

如果检查路径，您会发现名为 *csvdata.csv* 的文件。

就是这样。现在，您已经使用 R 语言将数据从 dataframe 导出到 CSV 文件。