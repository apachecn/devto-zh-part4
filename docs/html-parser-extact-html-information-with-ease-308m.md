# HTML 解析器——轻松提取 HTML 信息

> 原文：<https://dev.to/sm0ke/html-parser-extact-html-information-with-ease-308m>

你好编码器，

本文展示了一些使用 Python / BS4 库中编写的 [HTML 解析器](https://appseed.us/developer-tools/html-parser)提取和处理 HTML 信息的实用代码片段。将涵盖以下主题:

*   html 的负载
*   ✅扫描文件的资产:图像，Javascript 文件，CSS 文件
*   ✅改变现有资产的路径
*   ✅更新现有元素:更改图像的 src 属性
*   ✅根据 id 定位元素
*   ✅从 DOM 树中移除一个元素
*   ✅处理现有组件:删除硬编码文本
*   ✅将处理后的 HTML 保存到一个文件中

* * *

## 什么是 [HTML 解析器](https://appseed.us/developer-tools/html-parser)

根据维基百科，[解析](http://en.wikipedia.org/wiki/Parsing)或句法分析是根据正式语法的规则分析一串符号的过程，无论是自然语言还是计算机语言。这里应用的 **HTML 解析**的意思是加载 HTML，提取并处理相关信息，如标题、页面资产、主要章节等，并保存处理后的文件。

* * *

## 解析器环境

代码使用了 [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup) 库，这是用 Python 编写的著名解析库。为了开始编码，我们需要在我们的系统上安装一些模块。

```
$ pip install ipython # the console where we execute the code
$ pip install requests # a library to pull the entire HTML page
$ pip install BeautifulSoup # the real magic is here 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 加载 HTML 内容

该文件将像任何其他文件一样被加载，并且内容应该被注入到一个 BeautifulSoup 对象

```
from bs4 import BeautifulSoup as bs

# Load the HTML content html_file = open('index.html', 'r')
html_content = html_file.read()
html_file.close() # clean up 
# Initialize the BS object soup  = bs(html_content,'html.parser') 
# At this point, we can interact with the HTML 
# elements stored in memory using all helpers offered by BS library 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 解析 HTML 中的资产

此时，我们已经在 BeautifulSoup 对象中加载了 DOM 树。让我们在 DOM 树中扫描 Javascript 文件，脚本节点:

```
...
<script type='text/javascript' src='js/bootstrap.js'></script>
<script type='text/javascript' src='js/custom.js'></script>
... 
```

Enter fullscreen mode Exit fullscreen mode

定位 Javascript 的代码片段只有几行代码。BS 库将返回一个对象数组，我们可以轻松地改变每个脚本节点:

```
for script in soup.body.find_all('script', recursive=False):

   # Print the src attribute
   print(' JS source = ' + script['src'])

   # Print the type attribute
   print(' JS type = ' + script['type']) 
```

Enter fullscreen mode Exit fullscreen mode

以类似的方式，我们可以选择和处理 CSS 节点:

```
...
<link rel="stylesheet" href="css/bootstrap.min.css">
<link rel="stylesheet" href="css/app.css">
... 
```

Enter fullscreen mode Exit fullscreen mode

代码呢..

```
for link in soup.find_all('link'):

   # Print the src attribute
   print(' CSS file = ' + script['href']) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 解析图片的 HTML

在这段代码中，我们将改变节点，并更改图像节点
的`src`属性

```
...
<img src="images/pic01.jpg" alt="Bred Pitt">
... 
```

Enter fullscreen mode Exit fullscreen mode

```
for img in soup.body.find_all('img'):

   # Print the path 
   print(' IMG src = ' + img[src]) 

   img_path = img['src']
   img_file = img_path.split('/')[-1] # extract the last segment, aka image file  
   img[src] = '/assets/img/' + img_file 
   # the new path is set 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 根据 ID 定位元素

这可以通过一行代码来实现。假设我们有一个 id 为`1234` :
的元素(div 或 span)

```
...
<div id="1234" class="handsome">
Some text
</div> 
```

Enter fullscreen mode Exit fullscreen mode

还有代码:

```
mydiv = soup.find("div", {"id": "1234"})

print(mydiv) 

# delete the element mydiv.decompose() 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 删除硬编码文本

这个代码片段对于组件提取和翻译到不同的模板引擎非常有用。假设我们有这个简单的组件:

```
<div id="1234" class="cool">
   <span>Html Parsing</span>
   <span>the practical guide</span> 
</div> 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想在 Php 中使用这个组件，组件就变成:

```
<div id="1234" class="cool">
   <span><?php echo $title ?></span>
   <span><?php echo $info ?></span> 
</div> 
```

Enter fullscreen mode Exit fullscreen mode

或者为 Jinja2 (Python 模板引擎)

```
<div id="1234" class="cool">
   <span>{{ title }}</span>
   <span>{{ info }}</span> 
</div> 
```

Enter fullscreen mode Exit fullscreen mode

为了避免手工操作，我们可以使用代码片段来自动替换硬编码的文本，并为特定的模板引擎准备组件:

```
# locate the div mydiv = soup.find("div", {"id": "1234"})

print(mydiv) # print before processing 
# iterate on div elements for tag in mydiv.descendants:

   # NavigableString is the text inside the tag, 
   # not the tag himself 
   if not isinstance(tag, NavigableString):

      print( 'Found tag = ' + tag.name ' -> ' + tag.text )
      # this will print:
      # Found tag = span ->  Html Parsing
      # Found tag = span ->  the practical guide 
      # replace the text for Php
      tag.text = '<?php echo $title ?>'

      # replace the text for Jinja
      tag.text = '{{ title }}' 
```

Enter fullscreen mode Exit fullscreen mode

要使用组件，我们可以将组件保存到一个文件:

```
 # mydiv is the processed component php_component is the string representation
php_component = mydiv.prettify(formatter="html") 

file = open( 'component.php', 'w+') 
file.write( php_component )
file.close() 
```

Enter fullscreen mode Exit fullscreen mode

此时，原始的 div 从 DOM 中提取出来，删除了硬编码的文本，可以在 Php 或 Python 项目中使用了。

* * *

## 保存新 HTML

现在我们在内存中有了一个漂亮的对象。要将内容保存到新文件中，我们需要调用`prettify()`并将内容保存到新的 HTML 文件中。

```
 new_dom_content = soup.prettify(formatter="html") 

file = open( 'index_parsed.html', 'w+') 
file.write( new_dom_content )
file.close() 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## [HTML 解析器](https://appseed.us/developer-tools/html-parser) -用例

我经常使用 HTML 解析，尤其是对于涉及手工操作的任务:

*   处理要在新项目中使用的 HTML 主题
*   提取硬编码文本并提取组件
*   将平面 HTML 主题翻译成 Jinja、Mustache 或 PUG 模板

有时，我会在这个公共的库中发布免费的样本。

## 资源

*   [HTML 解析器](https://appseed.us/developer-tools/html-parser) -由 AppSeed 支持
*   [HTML 解析器——如何用 Python BS4 少干活](https://blog.appseed.us/html-parser-python-beautifulsoup-use-cases/)
*   [开发者工具-开源 HTML 解析器](https://blog.appseed.us/developer-tools-html-parser/) -相关文章
*   [BeautifulSoup Html 解析器](https://www.crummy.com/software/BeautifulSoup)文档
*   HTML 解析器-将 HTML 转换成 Jinja2 和 Php 组件 -相关博客文章

* * *

> 谢谢大家！顺便说一下，我的(昵称)名字是 **Sm0ke** ，我在[推特](https://twitter.com/Sm0keDev)上也很活跃。