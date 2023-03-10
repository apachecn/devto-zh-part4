# Elm 中 BMP 图像文件的解码

> 原文：<https://dev.to/viir/bmp-image-file-decoding-in-elm-2859>

最近我在 Elm 中实现了一个 BMP 图像解码器。这个 Elm 函数将图像作为字节数组( [`Bytes.Bytes`](https://github.com/elm/bytes) )并返回该图像中像素的值。

在当前的项目中，我在寻找一种方法来解码 Elm 中的二维光栅图像，以获得图像中单个像素的值。我在 Elm 论坛上搜索现有的解决方案，但是没有任何结果。所以我从头开始构建了一个 BMP 图像文件解码器。

那么到底是什么情况呢？用户将包含截图的 RGB 图像文件加载到我们的程序中。然后，程序需要读取单个像素的红色、绿色和蓝色分量的强度，以便进一步处理。此外，用户还应该能够将相同的图像文件加载到流行的光栅图形编辑器中，如 MS Paint。由于这个限制，我不能使用最简单的图像文件格式。相反，我需要支持一种流行的文件格式，如 PNG、BMP 或 JPEG。

仔细观察这些图像文件格式，BMP 似乎是实现解码器最简单的格式。我发现维基百科[关于 BMP 文件格式的文章](https://en.wikipedia.org/wiki/BMP_file_format)解释得很好。在那篇文章中，我们还可以看到 BMP 支持多种像素格式。但是对于我们的应用程序，我们只需要支持 24 位像素(24bpp)格式。

尽管支持的格式相对有限，但我预计在编写解码器实现时会有很多机会引入错误。所以从一开始就很清楚，我需要对解码器进行一些测试，以确保它能够正常工作。我从一些图像文件的编码测试开始。这些测试是用 Elm 编写的，所以我们可以用 elm-test 工具运行它们，看看解码功能是否正常工作。测试在一侧模拟平面文件，在另一侧模拟像素值。参考图像文件被编码为 base64 字符串，以便将它们放入 Elm 源代码。测试模块发布在[https://github . com/Viir/bots/blob/82ef 37 f 3089 a 3e 40 BD 496 f 507 b 691 a5c 25011 b 33/implement/dev tools/read-from-image/tests/decodebmpimagetest . elm](https://github.com/Viir/bots/blob/82ef37f3089a3e40bd496f507b691a5c25011b33/implement/devtools/read-from-image/tests/DecodeBMPImageTest.elm)

为了找出解码方法，我发现维基百科的文章很容易理解。让我吃惊的一件事是榆树图书馆里`Bytes.Decode.decode`的行为。该函数可以为相同的输入返回不同的结果，因为它[抑制堆栈溢出](https://github.com/elm/bytes/issues/9)。

从图像文件中获取 RGB 像素值的最终图像解码函数发布在[https://github . com/Viir/bots/blob/82ef 37 f 3089 a3 e 40 BD 496 f 507 b 691 a5c 25011 b 33/implement/dev tools/read-from-image/src/decodebmpimage . elm](https://github.com/Viir/bots/blob/82ef37f3089a3e40bd496f507b691a5c25011b33/implement/devtools/read-from-image/src/DecodeBMPImage.elm)

(本帖原载于[https://michaelrtzel . com/blog/BMP-image-file-decoding-in-elm](https://michaelr%C3%A4tzel.com/blog/bmp-image-file-decoding-in-elm))