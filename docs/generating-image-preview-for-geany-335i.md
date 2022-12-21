# 为 geany 生成图像预览

> 原文：<https://dev.to/suntong/generating-image-preview-for-geany-335i>

这是我上一篇博客[轻松生成图像预览](https://dev.to/suntong/generating-image-preview-easily-opj/)的后续，该博客从总体上谈论了图像预览库自动生成*。它讲述了 Go 模板引擎有多强大(这也是`easygen`所基于的)，以及定制自动生成有多容易。*

 *现在，根据对我的 PR 的审查，对 geany 的新要求是，

> 如果标题直接链接到主题文件，这样就可以直接下载到用户的主题目录，然后让图片链接到 PNG 文件，会怎么样？

啊哈，两个“花式”变化，我喜欢挑战，会再加一个-

链接主题文件有*两种*方式，*一种*是链接到其 github 页面地址；另一个是链接到 githubusercontent.com 的原始文件，按原样显示文件。

此外，一个进一步的要求是，一个图像，不完全是一个主题截图，应该从输出中删除(T0)。

解决方案仍然很简单。

*   要从`README.md`中删除不必要的图像，将其从驱动数据中删除:

    ```
    ls *.png | grep -v screenshot-missing | sed 's/^/  - /; 1s/^/Images:\n/; ' | tee /tmp/Images.yaml 
    ```

*   更新后的模板只是:*