# Ruby 中最受欢迎的调试技巧

> 原文：<https://dev.to/cassidycodes/favourite-debugging-trick-in-ruby-ljg>

上周我写了一些我最喜欢的 Ruby 调试工具。我忘记了一个非常有用的技巧！

今天我写了一个脚本，它将遍历 S3 桶中的对象，并按相似的名称对这些对象进行分组。命名结构如下:

```
original.jpg
tumb_original.jpg
profile_original.jpg 
```

Enter fullscreen mode Exit fullscreen mode

所以这三张图片应该组合在一起，因为它们都来自于`orignal.jpg`。但是我把我的名字比较弄混了，有些地方不太对劲。它可以完美地处理 200 张图片，但是在第 201 张的时候就爆炸了！

像这样打印日志或者将绑定放入一个循环中是很麻烦的。我不想通过 200 次成功的迭代来找到我需要的东西！

## 条件绑定！

为了解决这个问题，我只是使我的绑定有条件。

```
s3_objects.each do |img|
  binding.pry if img.key =~ /badfilename.jpg/
end 
```

Enter fullscreen mode Exit fullscreen mode

这一次，我们将只点击绑定，如果图像键包含给我带来痛苦的文件名！