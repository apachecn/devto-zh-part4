# 用 iPrint 给你的控制台上色

> 原文：<https://dev.to/aj1thkr1sh/color-you-console-with-iprint-4f3p>

# iPrint

Hi，cosole.log(值)；
很酷😎但是它是非常庞大的语法。
和，

如果非要做打字该有多酷

```
 iPrint('Hi'); 
```

还有给控制台增色？
😲

## [T1】给你！](#here-you-are)

iPrint 是很酷的控制台记录器。

iPrint 给你的“控制台”上色！

## 命令行用法

为了 NPM

```
 npm install iprint 
```

或者

```
 npm i iprint 
```

# 举例

```
 var iPrint = require('iPrint').iPrinter;

  iPrint('Hi, everyone');

  iPrint('Hi everyone - Magenta Color','magenta');

  iPrint('Hi, everyone - Blue Background','bgblue');

  iPrint('iPrintDefaultColor','cyan'); // Changes default output color from 'blue' to 'cyan'

  iPrint('Hi, default color changed form blue to cyan'); 
```

## 通用语法

只需键入如下内容

..1.只打印字符串(默认颜色是蓝色)

```
 iPrint(value); 
```

..2.给你的琴弦添加色彩

```
 iPrint(value,colorValue); 
```

..3.向字符串添加背景色

```
 iPrint(value,backgroundColorValue); 
```

..4.要更改字符串的默认颜色

```
 iPrint('iPrintDefaultColor',colorValue); 
```

“iPrintDefaultColor”是更改输出字符串默认颜色的严格表示法。

这里输出后的默认颜色将被改变。

# 颜色值

```
 iPrint(value,colorValue); 
```

1.  colorValue = '蓝色'
2.  colorValue = 'red '
3.  colorValue = '绿色'
4.  colorValue = '黄色'
5.  colorValue = '青色'
6.  colorValue = '白色'
7.  colorValue = '黑色'
8.  colorValue = 'magenta '

# 背景颜色值

```
 iPrint(value,backgroundColorValue); 
```

1.  colorValue = 'bgblue '
2.  colorValue = 'bgred '
3.  colorValue = 'bggreen '
4.  colorValue = 'bgyellow '
5.  colorValue = 'bgcyan '
6.  colorValue = 'bgwhite'
7.  colorValue = 'bgblack '
8.  colorValue = 'bgmagenta '