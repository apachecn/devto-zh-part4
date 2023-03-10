# 美丽的汤你好世界

> 原文：<https://dev.to/vicentemaldonado/beautiful-soup-hello-world-5b1k>

[![](img/510f6b0aa0018962f47985887179864e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rNbOfGFf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/450/1%2A_pugNfCqCKDV4utB85rJtw.jpeg)

Beautiful Soup 是一个用于处理 HTML 和 XML 文件的 Python 库。你可以用它来导航一个 HTML 文档，搜索它，从中提取数据，甚至改变文档结构。让我们来看看它是如何工作的:

```
from bs4 import BeautifulSoup

html = '''
    <html>
    <head>
        Beautiful Soup Hello World
    </head>
    <body>
        <h1>Header</h1>
        <p>Paragraph 1</p>
        <p>Paragraph 2</p>
        <p>Paragraph 3</p>
    </body>
    </html>
'''

soup = BeautifulSoup(html, 'html.parser')

print(soup.title)
print(soup.title.name)
print(soup.title.text)

print(soup.p.text)

for paragraph in soup.find\_all('p'):
    print(paragraph.text)

print(soup.get\_text()) 
```

这是一个非常基本的例子，但是在运行它之前，您首先需要安装 Beautiful Soup:

```
pip install beautifulsoup4 
```

同时，还要安装另一个库:

```
pip install lxml 
```

这是一个 HTML/XML 解析器。别担心。

让我们开始—导入美汤:

```
from bs4 import BeautifulSoup 
```

接下来，我们需要一些 HTML 来处理:

```
html = '''
    <html>
    <head>
        Beautifulsoup Hello World
    </head>
    <body>
        <h1>Header</h1>
        <p>Paragraph 1</p>
        <p>Paragraph 2</p>
        <p>Paragraph 3</p>
    </body>
    </html>
''' 
```

它是一个存储在 Python 字符串中的基本 HTML 文档。当然，使用存储在 Python 脚本中的 HTML 并不令人兴奋，但这是一个 Hello，World，所以嘿。

创建一个 BeautifulSoup 对象的实例，指定要使用的 HTML 文档和解析器(我说过不用担心):

```
soup = BeautifulSoup(html, 'html.parser') 
```

现在我们已经将 HTML 解析并存储在一个名为 soup 的变量中，我们可以使用它:

```
print(soup.title) 
```

使用 soup.title 访问 HTML 文档的

element. This prints:

```
Beautifulsoup Hello World 
```

有时候你不想要 HTML 标签:

```
print(soup.title.text) 
```

并且只获得元素文本:

```
Beautifulsoup Hello World 
```

我们的文档只有一个

element so Beautiful Soup appropriately returns it if we use soup.title. But the documents has three

元素(段落)那么当我们试着玩同样的把戏时会发生什么呢？

```
print(soup.p.text) 
```

它返回第一个

文档中的元素:

```
Paragraph 1 
```

如果你想得到一个文档中的所有段落，那么，只需使用 find_all():

```
for paragraph in soup.find\_all('p'):
    print(paragraph.text) 
```

find_all()返回文档中的所有段落，您可以使用一个简单的 for 循环迭代它们。

这只是用漂亮的汤来擦擦表面。最后，让我们看看获取文档中的所有文本(并且只有文本)是多么简单:

```
print(soup.get\_text()) 
```

不出所料，这会打印出

```
Beautifulsoup Hello World

Header
Paragraph 1
Paragraph 2
Paragraph 3 
```

你可以在我的 Github 中找到完整的脚本。ttfn。