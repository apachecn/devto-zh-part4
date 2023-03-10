# 面向 p5.js 鉴赏家的 CodeGuppy

> 原文：<https://dev.to/codeguppy/codeguppy-for-p5-js-connoisseurs-243b>

# 什么是 CodeGuppy？

codecuppy.com 是一个在线环境，儿童、青少年和有创造力的成年人通过创造性的编码学习和练习 JavaScript。

CodeGuppy 基于流行的 [p5.js](https://p5js.org/) 、 [p5.play](http://molleindustria.github.io/p5.play/) 和 [p5.scenemanager](https://github.com/mveteanu/p5.SceneManager) 库。

因为 CodeGuppy 是面向教育的，所以在上面提到的库的基础上增加了一些额外的扩展。

这篇文章是为:

*   熟悉 p5.js 并开始使用 CodeGuppy 的用户
*   希望从 CodeGuppy 升级到 p5.js 的用户

# 没有设置()

熟悉 p5.js 的用户知道，他们必须在草图中定义的第一个函数是`setup()`函数。

使用 CodeGuppy，不再需要 p5.js `setup()`函数。如果你试图定义它，它将被忽略。作为一个教育环境，CodeGuppy 自动为程序做一些决定，以便简化初学者的编码。

CodeGuppy 自动将画布初始化为 800x600 分辨率。

```
circle(400, 300, 200); 
```

# 温柔编程入门

在一个典型的 p5.js 草图中，用户通常在至少定义了`setup()`函数之后键入他们自己的代码。

由于不需要在 CodeGuppy 中定义`setup()`函数，所以学生可以循序渐进地学习编程。

一旦掌握了 JavaScript 的基本知识，就可以在课程的后面介绍函数。

```
stroke("Navy");

for(var i = 0; i < 100; i++)
{
    circle(400, 300, i);
} 
```

# 丰富的绘图 API

CodeGuppy 用一些新的函数丰富了 p5.js 绘图 API，这些函数更容易被年轻的程序员使用和理解。

例如:`circle()`

你知道吗，只用一个功能就可以获得很多乐趣？检查从第一节 CodeGuppy 课中提取的代码。

```
// Draw bear face
circle(400, 300, 200);

// Draw left year
circle(250, 100, 50);
circle(270, 122, 20);

// Draw right year
circle(550, 100, 50);
circle(530, 122, 20);

// Draw left eye
circle(300, 220, 30);
circle(315, 230, 10);

// Draw right eye
circle(500, 220, 30);
circle(485, 230, 10);

// Draw nose
circle(400, 400, 90);
circle(400, 350, 20); 
```

# CodeGuppy 默认值

CodeGuppy 将 p5.js 的一些默认值更改为不同的值。

例如:

*   程序初始化时没有定义填充颜色。试着画几个形状，你会发现它们是透明的
*   默认角度模式更改为度。p5.js 草图使用弧度作为默认值。

```
background("Navy");
stroke("White");

// Notice that ellipse is transparent
ellipse(400, 300, 300, 300); 
```

# 内置资产

CodeGuppy 自带内置资产。这使得用户可以专注于编写代码，而不是组织项目或管理资产。这就是“键入”程序的功能。

有四组资产。每个资产都有一个唯一的名称。

*   背景
*   鬼怪；雪碧
*   音乐
*   音效

以下 CodeGuppy 函数可以处理资产。它们接收资产名称作为参数。

*   `background()`
*   `sprite()`
*   `music()`
*   `sound()`

因为在程序执行之前需要预加载资产，所以 CodeGuppy 环境在执行之前解析代码，以识别程序使用了哪些资产来加载它们。

在大多数情况下，用户对这些函数使用静态名称。如果使用变量，CodeGuppy 将无法计算出所使用的资产。在这种情况下，用户必须调用`preload()`函数，后面会解释。

```
background('Scene1');
sprite('plane');
music('8 bit retro');

sound('female_congratulations'); 
```

# 小精灵

CodeGuppy 附带了一个预先选择的精灵库。许多包含的精灵是动画。CodeGuppy 运行时通过利用 p5.play 库来处理适当的动画显示。

然而，为了便于使用，一些函数和扩展是在这个库的基础上构建的。

要加载和显示一个 sprite，只需要调用`sprite()`函数，将 sprite 名称作为参数传递。

您还可以指定子画面的大小、要显示的动画以及子画面在屏幕上最初显示的位置。显然，您可以在以后更改这些内容。

```
sprite("plane").velocity.x = 1; 
```

# 预压资产

在大多数情况下，用户使用带有静态参数的背景、音乐、声音、精灵。这允许 CodeGuppy 环境确定程序使用的资产并自动预加载它们。

然而，如果用户想让代码在运行时决定使用什么资产，那么他必须使用`preload()`函数声明程序使用的所有资产。这可以出现在程序的任何部分，尽管建议将其放在程序的开头。

```
preload("Scene1", "Scene2");

var scene = random(["Scene1", "Scene2"]);
background(scene); 
```

# 键入程序

有了 CodeGuppy，键入程序又回来了！流行于 70 年代和 80 年代的打字程序是包含在杂志或其他印刷材料中的小程序，需要用户从键盘输入。

虽然这种输入程序的方法时间效率不是很高，但从教育的角度来说是非常好的。输入程序的用户会更快地适应这种语言，并最终更快地学会编程。

毕竟，编码就是用键盘输入程序。

CodeGuppy 如何启用键入程序？非常简单:所有的资产和库都包括在内！用户不需要关注项目组织或资产管理。他们只需要打开代码编辑器，输入程序。

> 教育工作者和家长可以利用这一功能，将打印的代码清单交给孩子，这样他们就可以练习打字了。

```
// This program can be found in a magazine...
background('Icebergs');
sprite('santa', 0.9);

stroke("Red")
textSize(30);
text("We wish you a Merry Christmas!", 150, 30); 
```

# 5 层

CodeGuppy 程序使用多达 5 层:

*   背景
*   精灵们。深度< = 0
*   绘图层
*   精灵们。深度> 0
*   HTML 小工具

背景层绘制在底部，所有其他层的对象添加在顶部。

```
// Layer 1 (Bottom layer)
background('Autumn');

// Layer 2 (sprites with .depth <= 0)
var cat = sprite('cat', 200, 300)
cat.depth = -1;

// Layer 4 (sprites with .depth > 0)
var dog = sprite('dog', 600, 300);
dog.depth = 1;

// Layer 5 (Top layer. HTML5 widgets)
var btn = createButton(650, 560, 100, 30);
btn.setText("Click me");

// Layer 3 (drawing)
fill("White");

function loop()
{
    clear();

    circle(mouseX, mouseY, 100);
} 
```

# 设置背景

`background()`功能在背景层运行。

该函数也得到丰富，可以接受更多的值作为参数。例如，`background()`可以与资源库中的颜色或静态背景一起使用。

> 注意:因为`background()`只在底层操作，所以不能使用该功能擦除`loop()`事件内的绘图层。为此，你需要使用`clear()`。

```
background("Hearts");

// background("Pink");

// background("#104210"); 
```

# 游戏和动画

构建游戏和动画最流行的方式是实现“游戏循环”模式。在 p5.js 草图中，游戏循环是通过`draw()`函数实现的。

在 CodeGuppy 中，这个函数被命名为`loop()`而不是`draw()`。如果您要将 p5.js 草图移植到 CodeGuppy，请确保将您的`draw()`函数重命名为`loop()`。

同样——因为`background()`效果只在底层可见，你不能在`loop()`中使用`background()`调用来清除屏幕。相反你需要叫`clear()`

```
var y = 0;

background("Azure");

function loop()
{
    clear();

    circle(400, y, 10);

    y++;

    if (y > height)
        y = 0;
} 
```

# 多场景游戏

场景管理是构建游戏时的常见话题。即使是最基本的游戏也至少有三个场景:介绍场景、主游戏场景和演职员表场景。

有了 CodeGuppy，初学者可以快速构建这些多场景游戏，而不必担心场景管理的细节问题。

编辑器本身将每个场景放在一个单独的代码页中。您可以定义任意多的场景。在执行时，执行第一场景代码。要切换场景，您可以使用友好的`showScene()`函数提供您想要跳转到的场景的名称。

您可以选择将参数传递给正在切换的新场景。

要在显示场景时执行初始化操作，您可以在每个场景中定义一个`enter()`函数。当显示该场景时，将自动调用该函数。

## 场景:简介

```
function enter()
{
    background("Scene1");
    textSize(30);
    text("Click to begin...", 300, 570);
}

function mouseClicked()
{
    showScene("Game");
} 
```

## 场景:游戏

```
background("Scene2");
function loop()
{
    line(pmouseX, pmouseY, mouseX, mouseY);
}

function mouseClicked()
{
    showScene("Credits", [mouseX, mouseY]);
} 
```

## 场景:演员表

```
function enter()
{
    background("Hearts2");
    textAlign(CENTER, CENTER);

    textSize(30);
    text("Congratulations", width / 2, height / 2);

    if (PublicVars.Arguments)
    {
        textSize(10);
        text("Last known coordinates: " + PublicVars.Arguments[0] + " x " + PublicVars.Arguments[1], width / 2, height / 2 + 50);
    }
}

function mouseClicked()
{
    showScene("Intro");
} 
```

# 模块

如前所述，CodeGuppy 编辑器中的代码页主要用于为场景编写代码。

代码页的第二个用途是定义代码模块。这种用法旨在用于高级编码。代码模块可以通过`require()`函数从剩余的代码中使用。

> 注意:`require()`函数在每次调用时都执行该模块的代码。

## 主代码

```
var utils = require("Utils");

var r1 = utils.add(100, 200);
println(r1);

var r2 = utils.mult(100, 200);
println(r2); 
```

## Utils

```
function add(n1, n2)
{
    return n1 + n2;
}

function mult(n1, n2)
{
    return n1 * n2;
} 
```

# 支持“基于文本的输出”程序

当教授编码或构建算法/数学程序时，将结果打印在屏幕上非常方便。

CodeGuppy 允许用户通过`print()`和`println()`函数快速打印数字和字符串。

> 注意:这些函数打印在 HTML5 透明区域的最顶层。该区域是可滚动的，以便容纳任意数量的行。一旦打印层显示出来，你就不能隐藏它…因此你需要提前决定你的程序是需要基于文本的输出还是图形输出。
> 
> 注意:由于打印层是透明的，所以可以使用`background()`功能为枯燥的程序设置趣味背景。

```
background("Fabric2");

for(var i = 0; i < 10; i++)
{
    println("i = " + i);
} 
```

# 支持“输入”程序

如果你正在构建字符串处理程序，或者甚至是一些游戏，你经常需要请求用户输入。例如，使用键盘输入长文本。

CodeGuppy 提供了两个非常简单的 API 来显示文本框和按钮。

这两个功能是`createTextBox()`和`createButton()`。它们在最顶层操作，因此它们显示在图形画布的顶部。

```
var edit1 = createEdit(100, 100, 500, 30);
edit1.setText("I love Codeguppy!");

var btn1 = createButton(100, 150, 100, 30);
btn1.setText("Click Me");
btn1.onclick = handleButton1Click;

function handleButton1Click()
{
    var txt = edit1.getText();
    alert(txt);
} 
```

# 其他 API

CodeGuppy 还包含其他有用的 API，帮助您轻松构建游戏和有趣的应用程序。例如，用户可能会发现用于碰撞检测的 API 特别有用:

*   `collisionPointCircle(pointX, pointY, circleX, circleY, circleR)`
*   `collisionPointLine(pointX, pointY, lineX1, lineY1, lineX2, lineY2)`
*   `collisionCircleCircle(circle1X, circle1Y, circle1R, circle2X, circle2Y, circle2R)`
*   `collisionCircleRect(circleX, circleY, circleR, rectX, rectY, rectWidth, rectHeight)`
*   `collisionPointRect(pointX, pointY, rectX, rectY, rectWidth, rectHeight)`
*   `collisionRectRect(rect1X, rect1Y, rect1Width, rect1Height, rect2X, rect2Y, rect2Width, rect2Height)`

注意:为了方便起见，一些碰撞函数被复制:

*   `collisionCirclePoint(circleX, circleY, circleR, pointX, pointY)`
*   `collisionLinePoint(lineX1, lineY1, lineX2, lineY2, pointX, pointY)`
*   `collisionRectCircle(rectX, rectY, rectWidth, rectHeight, circleX, circleY, circleR)`
*   `collisionRectPoint(rectX, rectY, rectWidth, rectHeight, pointX, pointY)`

# 更多信息

欲了解更多信息，请访问[codecuppy.com](https://codeguppy.com)