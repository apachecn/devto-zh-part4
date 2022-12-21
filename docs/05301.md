# 让我们用 Python 和 OpenCV 在图像中隐藏秘密消息

> 原文：<https://dev.to/erikwhiting88/let-s-hide-a-secret-message-in-an-image-with-python-and-opencv-1jf5>

今天我们将使用 OpenCV 库`cv2`对图像文件中的秘密信息进行编码和解码。我的意思不是说我们要制作微小的文本并把它藏在角落里，我们要挖掘得更深。启动你最喜欢的编辑器和 Python 控制台，这实际上不会花太长时间。

# 我们开始之前

如果你不想坐着听我谈论这是如何工作的，什么是隐写术，那就直接来听我的 Github 要诀[这里](https://gist.github.com/erik-whiting/d9988122b5e94b1db4c15236b6d86975)

## 这是怎么回事？？

很高兴你问了。首先，你需要一个快速的像素速成班。你的屏幕是由像素组成的，可能是相当多的像素。我的显示器分辨率显示“1920x1080”，这意味着我的屏幕宽 1920 像素，深 1080 像素，总共是 2，073，600 像素。这些像素中的每一个都能够发射一个颜色范围内的光。它发出的光的颜色(通常)是由红色、绿色和蓝色混合而成的。每种颜色的强度可以从 0 到 255。换句话说，RGB(255，255，255)是白色，RGB(0，0，0)是黑色，RGB(113，238，184)是海泡石绿色。

图像文件基本上是图像像素和 RGB 值的序列化。该文件告诉计算机哪些像素点亮，用什么颜色点亮。当我们使用`cv2`函数`imread`并向其传递一个图像文件时，该图像文件被转换成一个 numpy 数组，其中包含图像中每个像素的**的 RGB 值。**

举例来说，想象我们有一个 4 像素的小图片(2 像素乘 2 像素),是一个 4 块黑白棋盘。numpy 数组本质上应该是这样的:

```
[
  [
    [255, 255, 255], [0, 0, 0]
  ],
  [
    [0, 0, 0], [255, 255, 255]
  ]
] 
```

Enter fullscreen mode Exit fullscreen mode

*注意:这不是一个精确的复制，有时数组中还有其他数据，但这足以让您理解*

这是你的像素速成班，我们一会儿再回来。

## 什么是隐写术？

隐写术是将秘密数据隐藏在非秘密数据中的做法。大多数时候，我们谈论的是在图像文件中隐藏信息，但不一定是图像文件。假设我想和你分享一个秘方。我可以调出我最喜欢的你和我的照片，把食谱编码到照片里，然后贴在你的脸书墙上或者别的什么地方。对这个世界来说，看起来我只是给你发了一张很酷的照片。但是既然你有了解码器，你就可以通过它运行图像，现在你知道如何制作我祖母世界闻名的饼干了。

你现在明白这是什么了吗？很好！让我们付诸实践吧。

# 代码

## 编码

这是我们要做的。首先，我们将获取一条秘密消息，并用`ord()`函数将其翻译成 unicode 数字。我们将用一个返回 Python 生成器对象的函数来做这件事:

```
def char_generator(message):
  for c in message:
    yield ord(c) 
```

Enter fullscreen mode Exit fullscreen mode

*如果你不确定什么是发电机，不要太担心这节课。基本上，生成器会使用`next(generator)`* 一次给我们一个值

好吧，那很简单。现在，我们想要接收我们的图像，并将它们作为 numpy 数组返回。幸运的是，我们有 OpenCV `cv2`库。可以用`pip install opencv-python`和`pip install opencv-contrib-python`安装。安装完成后，将`import cv2`放在脚本的顶部。让我们写一个返回数字化图像的函数:

```
def get_image(image_location):
  img = cv2.imread(image_location)
  return img 
```

Enter fullscreen mode Exit fullscreen mode

简单。

所以现在我有了一个图片的 RGB 值的大数组，以及一个从字母中提取数字的生成器。我现在要做的是遍历图像文件，并用秘密消息的 unicode 编号更改三个 RGB 值中的一个值。

但是有一个问题。如果我遍历并按顺序改变每个像素，我就冒着改变图像太多的风险，很明显我已经改变了它。所以我绝对不想换一个又一个像素。

您决定选择要改变的像素的模式取决于您，但是我将使用图像数组的 GCD。请看:

```
def gcd(x, y):
  while(y):
    x, y = y, x % y

  return x 
```

Enter fullscreen mode Exit fullscreen mode

我们要做的就是选择 x 和 y 的最大公分母。我们要将图像的长度和宽度发送给这个函数，并使用它来决定我们要操作的像素。因此，如果 GCD 是 7，我们将抓取每七个像素，并在其中隐藏我们的秘密信息的下一部分。

不过还有另一个问题。我们完成后怎么告诉解码器？好吧，你可以做任何你想做的事情，但是我要把下一个要选择的像素设置为 0。由于 unicode 0 表示的字符不能在键盘上模仿，我们的解码器可以假设 0 表示消息结束。

好了，现在让我们来编写实际的图像编码函数。我们要把图像位置和秘密信息传递给它。然后，我们将为图像创建一个 numpy 数组，为秘密消息创建一个生成器对象，并计算图像的 GCD。最后，我们将编写有史以来最丑陋的嵌套循环来实际编码图片。观察:

```
def encode_image(image_location, msg):
  img = get_image(image_location)
  msg_gen = char_generator(msg)
  pattern = gcd(len(img), len(img[0]))
  for i in range(len(img)):
    for j in range(len(img[0])):
      if (i+1 * j+1) % pattern == 0:
        try:
          img[i-1][j-1][0] = next(msg_gen)
        except StopIteration:
          img[i-1][j-1][0] = 0
          return img 
```

Enter fullscreen mode Exit fullscreen mode

我知道，讨厌吧？注意关于这个函数的一些事情:我们检查并看到高度和宽度坐标的乘积可以被 GCD(`pattern`变量)整除。在此之前，我们实际上给迭代器加一。为什么？因为如果我们在高度或宽度的第一次迭代中，我们会乘以一个零。而`0 % anything`是 0，这意味着我们将只写入一列或一行。这将使图片被更改变得很明显。

无论如何，如果我们的像素是 GCD 的倍数，我们`try`得到秘密消息生成器中的下一个字符。当你在生成器生成所有东西后调用生成器上的`next`时，你会得到一个`StopIteration`异常，这就是我们的`except`块。当我们到达那个块时，我们知道消息结束了，所以我们把那个值设置为零。

这个功能不包括将新的编码图像保存到磁盘，所以让我们启动一个 python 控制台。使用 python 脚本文件(我称之为 mine stego.py)在目录中启动控制台，并准备好一个图像文件进行操作。我的名为“ErikAndFubar.png”(我的个人资料图片)。

```
>>> import stego, cv2
>>> file_location = "ErikAndFubar.png"
>>> secret_message = "Hello from inside the picture!"
>>> encoded_image = stego.encode_image(file_location, secret_message)
>>> cv2.imwrite("EncodedImage.png", encoded_image) 
```

Enter fullscreen mode Exit fullscreen mode

我们没有谈到`cv2.imwrite`但如果你不能告诉，这只是保存图像文件。保存好之后，去看看图片。我打赌你看不出有什么不同。

## 解码

现在我们已经对图片进行了编码，让我们来编写解码函数。我们将向函数传递文件位置，从中生成另一个 numpy 数组，计算出 GCD，这样我们就知道秘密位在哪里，然后再次遍历图片。喜欢 dis:

```
def decode_image(img_loc):
  img = get_image(img_loc)
  pattern = gcd(len(img), len(img[0]))
  message = ''
  for i in range(len(img)):
    for j in range(len(img[0])):
      if (i-1 * j-1) % pattern == 0:
        if img[i-1][j-1][0] != 0:
          message = message + chr(img[i-1][j-1][0])
        else:
          return message 
```

Enter fullscreen mode Exit fullscreen mode

这个循环比较容易理解，但也不容易理解。我们再次迭代 image-array，在 GCD 的倍数处停止，只要像素的红色值不为零，就获取它的数值。当我们得到这个值时，我们把`chr`的输出附加到它上面，它接受一个 unicode 数字并返回字母(所以`chr(103)`给我们`'g'`)。如果我们获取的值为零，我们知道我们已经到达了消息的末尾，所以我们返回得到的消息。

让我们在控制台中检查一下:

```
>>> import stego
>>> encoded_image = "EncodedImage.png"
>>> stego.decode_image(encoded_image)
'Hello from inside the picture!' 
```

Enter fullscreen mode Exit fullscreen mode

哒哒！

脚本的整体:

```
import cv2

def char_generator(message):
  for c in message:
    yield ord(c)

def get_image(image_location):
  img = cv2.imread(image_location)
  return img

def gcd(x, y):
  while(y):
    x, y = y, x % y

  return x

def encode_image(image_location, msg):
  img = get_image(image_location)
  msg_gen = char_generator(msg)
  pattern = gcd(len(img), len(img[0]))
  for i in range(len(img)):
    for j in range(len(img[0])):
      if (i+1 * j+1) % pattern == 0:
        try:
          img[i-1][j-1][0] = next(msg_gen)
        except StopIteration:
          img[i-1][j-1][0] = 0
          return img

def decode_image(img_loc):
  img = get_image(img_loc)
  pattern = gcd(len(img), len(img[0]))
  message = ''
  for i in range(len(img)):
    for j in range(len(img[0])):
      if (i-1 * j-1) % pattern == 0:
        if img[i-1][j-1][0] != 0:
          message = message + chr(img[i-1][j-1][0])
        else:
          return message 
```

Enter fullscreen mode Exit fullscreen mode

# 一些最终的想法

我们今天学习了如何在图片中隐藏秘密信息，但这就是我们所学的全部吗*？当我决定尝试学习如何做到这一点时，我不知道生成器，也不知道`chr`和`ord`函数。所以我从这个小项目中学到了一些东西，而不仅仅是我想做的事情。这就是为什么副业很重要。如果你让它们保持有趣，你可能会学到一些你甚至不知道存在的东西。*

此外，这不是一个非常好的编码器。我们总是改变 RGB(红色像素)的第一个值，而实际上我们应该切换它。你会如何改变？

在更基本的层面上，我们不做任何用户验证，比如检查是否有有效的图片位置被传递。我们也不检查我们的图片是否足够大来处理消息的长度。我将这些增强留给读者作为练习。

如果你有任何问题，请不要犹豫让我知道！