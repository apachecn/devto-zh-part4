# [ImageMagick]处理图像的有用命令

> 原文：<https://dev.to/chokri/imagemagick-useful-commands-to-process-images-20l0>

## 什么是 ImageMagick

许多人认为他们需要闭源软件来处理照片、创建 GIF 动画、色彩管理以及通过调整大小、裁剪和旋转来转换图像。

ImageMagick 能做的比我们预期的更多。它还可以读取和写入各种格式的图像，包括 PNG、JPEG、GIF、HEIC、TIFF、DPX、EXR、WebP、Postscript、PDF 和 SVG。

ImageMagick 是免费和开源的。它可以在 linux、mac 和 windows 上使用 [MinGW](http://www.mingw.org/) 操作系统。

它还可以 lib 格式制作 iOS、Windows、Linux 应用，并作为一个名为“php7.x-imagick”的 php 扩展来构建服务器端图像处理。

安装 ImageMagick 后，您应该可以访问它的所有工具。

## 识别

这个工具是提取图像信息所必需的，特别是使用-verbose param。这个命令将返回给定图像的所有信息。这里有一个例子:

```
identify -verbose beach-beautiful-bridge-449627.jpg
Image: beach-beautiful-bridge-449627.jpg
  Format: JPEG (Joint Photographic Experts Group JFIF format)
  Mime type: image/jpeg
  Class: DirectClass
  Geometry: 4032x3024+0+0
  Resolution: 72x72
  Print size: 56x42
  Units: PixelsPerInch
  Colorspace: sRGB
  Type: TrueColor
  Base type: Undefined
  Endianess: Undefined
  Depth: 8-bit
  Channel depth:
    Red: 8-bit
    Green: 8-bit
    Blue: 8-bit
  Channel statistics:
    Pixels: 12192768
    Red:
      min: 0  (0)
      max: 255 (1)
      mean: 70.7684 (0.277523)
      standard deviation: 67.0167 (0.262811)
      kurtosis: -0.402136
      skewness: 0.753672
      entropy: 0.888948
    Green:
      min: 0  (0)
      max: 255 (1)
      mean: 158.36 (0.62102)
      standard deviation: 58.2885 (0.228582)
      kurtosis: -0.658039
      skewness: -0.602582
      entropy: 0.962202
    Blue:
      min: 0  (0)
      max: 255 (1)
      mean: 209.097 (0.819987)
      standard deviation: 62.8143 (0.246331)
      kurtosis: 0.66187
      skewness: -1.37573
      entropy: 0.767422
  Image statistics:
    Overall:
      min: 0  (0)
      max: 255 (1)
      mean: 146.075 (0.572843)
      standard deviation: 62.7065 (0.245908)
      kurtosis: -1.24161
      skewness: -0.300494
      entropy: 0.872858
  Rendering intent: Perceptual
  Gamma: 0.454545
  Chromaticity:
    red primary: (0.64,0.33)
    green primary: (0.3,0.6)
    blue primary: (0.15,0.06)
    white point: (0.3127,0.329)
  Matte color: grey74
  Background color: white
  Border color: srgb(223,223,223)
  Transparent color: none
  Interlace: None
  Intensity: Undefined
  Compose: Over
  Page geometry: 4032x3024+0+0
  Dispose: Undefined
  Iterations: 0
  Compression: JPEG
  Quality: 73
  Orientation: Undefined
  Properties:
    date:create: 2019-08-30T09:12:12+00:00
    date:modify: 2019-05-03T13:10:37+00:00
    icc:copyright: Copyright (c) 1998 Hewlett-Packard Company
    icc:description: sRGB IEC61966-2.1
    icc:manufacturer: IEC http://www.iec.ch
    icc:model: IEC 61966-2.1 Default RGB colour space - sRGB
    jpeg:colorspace: 2
    jpeg:sampling-factor: 2x2,1x1,1x1
    signature: 567a0413315a5c6d41a4323b956452b234470449c7244448b2362c73411a0b57
  Profiles:
    Profile-icc: 3144 bytes
  Artifacts:
    verbose: true
  Tainted: False
  Filesize: 2.42723MiB
  Number pixels: 12.1928M
  Pixels per second: 57.8762MP
  User time: 0.200u
  Elapsed time: 0:01.210
  Version: ImageMagick 7.0.8-59 Q16 x86_64 2019-08-04 https://imagemagick.org 
```

Enter fullscreen mode Exit fullscreen mode

使用这个命令行，我们可以提取给定图像的颜色、像素、版权和许多信息。

## 内嵌图像调整大小

这是上传图库时在服务器上使用最多的命令。假设您从 SD 卡复制了一张原始图像，并希望将所有图像调整到特定的比例。您可以在文件夹:
中运行此命令

```
cd ~/Images mkdir folder magick '\*.jpg[widthxheight]' folder/%03d.jpg 
```

Enter fullscreen mode Exit fullscreen mode

## 内嵌图像裁剪

与调整大小一样，裁剪需要 X 和 Y 来定义哪个部分将开始裁剪:

```
magick '\*.jpg' -crop widthxheight+x+y thumbnail%03d.jpg 
```

Enter fullscreen mode Exit fullscreen mode

### 其他图像处理命令

有很多命令可以校正给定图像的伽玛、阿尔法、滤镜、模糊等。这些命令可用于[转换](https://imagemagick.org/script/convert.php)和[移动](https://imagemagick.org/script/mogrify.php)工具。还有-自动伽玛，-自动水平，-自动阈值，接受卡普尔和 OTSU 方法。你必须知道，你必须有一个图像处理的基本知识，帮助你做一个好的 magick 命令和处理最低限度的知识。所有这些命令在这里都有很好的记录和解释【https://imagemagick.org/script/command-line-options.php T4】。

这些命令的名字与背后的理论相同，所以如果你想的话，你可以学习一下。不要忘了 ImageMagick 是开源的和免费的，所以你可以找到使这些功能对你可用的代码。

帖子[【ImageMagick】处理图像的有用命令](https://khalifa.tn/imagemagick-useful-commands-to-process-images/)最早出现在 [C.Khalifa](https://khalifa.tn) 上。