# 不要用 Windows 记事本保存源代码

> 原文：<https://dev.to/shiena/windows-io7>

# problem

~~用 Windows 的记事本用 UTF-8 保存字符编码的话，一定会有 BOM。~~

这个 BOM 是作曲者，出现原因不明的编译错误，用差分比较工具在文件的开头出现神秘的差分，用 Git 进行二进制处理，这些都没有什么好事。

~~另外，在字符编码中选择 Unicode 进行保存的话，实际上会用 UTF-16 进行保存。
很容易混淆啊。~~

## Memories

[![image.png](img/e325fc0f4cbb938241be2a56e735d058.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iEe2vIOI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/sv0ty07sq46qw0bxij5z.png)
Windows 10 May 2019 Update 1903 的记事本中无 BOM utf-8 成为了默认的保存形式。
又容易混淆的 Unicode 变成了 UTF-16 LE 和 UTF-16 BE。

# Method of setting up

## Windows 2019 年 5 月 10 日更新 1903 以降

用记事本保存时用 UTF-8 保存。

## windows 2019 年 5 月 10 日更新 1903より前

很遗憾，记事本不能用无 BOM 的 UTF-8 保存。
为了避免不必要的纠纷，请避免使用记事本。

# Reference

*   [字节排序标记( BOM )](http://ja.wikipedia.org/wiki/%E3%83%90%E3%82%A4%E3%83%88%E3%82%AA%E3%83%BC%E3%83%80%E3%83%BC%E3%83%9E%E3%83%BC%E3%82%AF)
*   [记事本](http://ja.wikipedia.org/wiki/%E3%83%A1%E3%83%A2%E5%B8%B3)
*   [“记事本”有很多改进，无 BOM utf-8 成为默认保存形式~“windows 10 19 h1”-窗沿](https://forest.watch.impress.co.jp/docs/news/1157696.html)