# PHP 项目中的翻译

> 原文：<https://dev.to/ironangel2000/translations-in-php-projects-18d2>

有时需要将掩码、表单或其他模块翻译成其他语言。

经典的方法是创建一个单独的。php 文件，并为每个术语定义一个常量。这种变体的优点是常量在每个类和每个名称空间中都可用。

缺点是，如果必须定义许多常数，列表会变得混乱，翻译者不一定能理解这些常数。

一个快速且易于集成的小项目可以解决这个问题。

“索福克勒斯翻译”

Sophokles Translation 是一个库，可以通过 Composer 方便地安装和使用。

安装工作如下:

```
1\. composer require sophokles/translation

2\. Run composer install 
```

Enter fullscreen mode Exit fullscreen mode

现在可以通过 Composer 自动加载器获得新的类。

```
require_once '/vendor/autoload.php'; 
```

Enter fullscreen mode Exit fullscreen mode

这些类本身在名称空间“\Sophokles\Translation”中工作。

这些类处理单独的 JSON 格式化文件，术语在这些文件中被翻译。因为 JSON 存储在 UTF 8 代码页中，所以它可以用于所有语言。翻译者也可以阅读文件，用普通的文本编辑器轻松完成转换。

这种文件的一个例子:

```
{  "languages":{  "source":"en",  "target":"de"  },  "translations":[  {  "source":"save",  "target":"speichern"  },  {  "source":"cancel",  "target":"abbrechen"  },  {  "source":"delete",  "target":"löschen"  },  {  "source":"edit",  "target":"bearbeiten"  },  {  "source":"close",  "target":"schließen"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

“语言”部分定义源语言，而“目标”部分定义目标语言。翻译输入“翻译”部分。

在 PHP 中可以方便地集成 JSON 文件:

```
\Sophokles\Translation\translation::registerFile('/translations/lang.de.json'); 
```

Enter fullscreen mode Exit fullscreen mode

当然，可以包含几个文件，这样您就可以管理项目中的翻译模块。

翻译的输出由类\Sophokles\Translation\lang 处理。首先你必须定义目标语言(默认语言是“en”):

```
\Sophokles\Translation\lang::setLanguage('de'); 
```

Enter fullscreen mode Exit fullscreen mode

要输出一个术语，使用调用:

```
\Sophokles\Translation\lang::get([TERM]); 
```

Enter fullscreen mode Exit fullscreen mode

如果该类找到了该术语的翻译，则输出该术语，否则在其源中打印该术语。通过这种方式，您不必携带加密的密钥，您可以使用正确的单词直接在源语言中工作。这使得源代码对于其他程序员来说更具可读性。

索福克勒斯翻译还包括一个调试模式。简单定义常量“SOPHOKLES _ DEBUG”:

```
define ('SOPHOKLES_DEBUG', true); 
```

Enter fullscreen mode Exit fullscreen mode

如果常量被设置为“true ”,则系统会在每个找不到翻译的术语后输出一个波浪号“~”。

作曲家可从以下网址获得该库

[https://packagist.org/packages/sophokles/translation](https://packagist.org/packages/sophokles/translation)

或者对开发者来说

[https://github.com/ironAngel2000/sophokles-translation](https://github.com/ironAngel2000/sophokles-translation)