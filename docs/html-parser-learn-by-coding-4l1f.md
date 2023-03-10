# HTML 解析器——从实时网站中提取信息

> 原文：<https://dev.to/sm0ke/html-parser-learn-by-coding-4l1f>

你好编码器，

在这篇文章中，我将给出一个简短的列表，其中包含从一个实时网站中提取信息的有用代码片段。代码是在 [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/) [HTML 解析](http://en.wikipedia.org/wiki/Parsing)库之上用 Python 编写的。

* * *

> 谢谢大家！**内容由 [AppSeed - App 生成器](https://appseed.us)T3】提供。**

* * *

## 什么是 [HTML 解析器](https://appseed.us/developer-tools/html-parser)

根据维基百科，**解析**或句法分析是根据正式语法的规则分析一串符号的过程，无论是自然语言还是计算机语言。这里应用的 **HTML 解析**的意思基本上是，抓取 HTML 代码并提取、处理相关信息，如标题、页面资产、主要部分。

* * *

## 设置环境

为了执行示例代码，我们需要一个 Python 环境和一些有用的依赖项:

```
$ pip install ipython # the console where we execute the code
$ pip install requests # a library to pull the entire HTML page
$ pip install BeautifulSoup # the real magic is here 
```

Enter fullscreen mode Exit fullscreen mode

如果一切顺利，我们就可以开始编码了。请键入`ipython`启动交互式 Python 控制台:

```
# import libraries import requests
from bs4 import BeautifulSoup as bs

# define the URL to crawl & parse
# feel free to change this URL with your own app app_url = 'https://flask-bulma-css.appseed.us/'

# crawling the page. This might take a few seconds page = requests.get( app_url )

# to check the crawl status, just type: page
<Response [200]> # all good 
# to print the page contents type: page.content 
```

Enter fullscreen mode Exit fullscreen mode

此时，我们有了页面内容，让我们将 HTML 注入 BeautifulSoup 并从远程页面获取一些信息。

```
soup = bs(page.content, 'html.parser')

# print the entire page head soup.head

# print only the title soup.head.title
Flask Bulma CSS - BulmaPlay Open-Source App  
```

Enter fullscreen mode Exit fullscreen mode

为了检查结果的准确性，我们可以检查页面的

* * *

## 何去何从？

使用 BS 库我们可以很容易地操作 DOM。例如，让我们打印 HTML 文件使用的 Javascript 文件，只用几行代码:

```
 # the code for script in soup.body.find_all('script', recursive=False):
    print(' Js = ' + script['src'])

# the output
 Js = /static/assets/js/jquery.min.js
 Js = /static/assets/js/jquery.lazy.min.js
 Js = /static/assets/js/slick.min.js
 Js = /static/assets/js/scrollreveal.min.js
 Js = /static/assets/js/jquery.waypoints.min.js
 Js = /static/assets/js/jquery.waypoints-sticky.min.js
 Js = /static/assets/js/jquery.counterup.min.js
 Js = https://cdnjs.cloudflare.com/ajax/libs/modernizr/2.8.3/modernizr.min.js
 Js = /static/assets/js/app.js 
```

Enter fullscreen mode Exit fullscreen mode

* * *

让我们打印`app.js`文件的内容:

```
 # app_url is initialized a few line above:
# app_url = 'https://flask-bulma-css.appseed.us/' app_js = requests.get(app_url + '/static/assets/js/app.js')

# to check the status, just type the name of the object app_js
<Response [200]> # all good, let's print the content of the remote file 
app_js.content
# some unminified js code will be listed here. 
```

Enter fullscreen mode Exit fullscreen mode

* * *

让我们打印页面主体的第一级元素:

```
 # the code for elem in soup.body.children:
   if elem.name: # we need this check, some elements don't have name
      print( ' -> elem ' + elem.name )

# the output
 -> elem div
 -> elem section
 -> elem section
 -> elem section
 -> elem section
 -> elem section
 -> elem section
 -> elem footer
 -> elem div
 -> elem div
 -> elem div
 -> elem script
 -> elem script
 -> elem script
 -> elem script
 -> elem script
 -> elem script
 -> elem script
 -> elem script
 -> elem script 
```

Enter fullscreen mode Exit fullscreen mode

* * *

让我们打印页脚:

```
soup.footer

# to have a nice print of elements, we can use BS prettify() helper
# using prettify(), the output is nicely indented 
print(soup.footer.prettify())

# the output <footer class="footer footer-dark">
 <div class="container">
  <div class="columns">
   <div class="column">
    <div class="footer-logo">
     <img alt="Footer Logo for BulmaPlay - JAMStack Bulma CSS Web App." src="/static/asseimg/logos/bulmaplay-logo.png"/>
    </div>
....
    </div>
   </div>
  </div>
 </div>
</footer> 
```

Enter fullscreen mode Exit fullscreen mode

* * *

最后一段代码，让我们打印页脚部分引用的锚点:

```
 # the code for elem in soup.body.footer.find_all('a'):
    print(' footer href = ' + elem['href'])

# the output
 footer href = https://bulma.io
 footer href = https://github.com/app-generator/flask-bulma-css
 footer href = https://appseed.us/apps/bulma-css?flask-bulma-css
 footer href = https://blog.appseed.us/tag/bulma-css
 footer href = https://absurd.design/
 footer href = https://github.com/cssninjaStudio/fresh 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 相关文章

*   [开发者工具-开源 HTML 解析器](https://blog.appseed.us/developer-tools-html-parser/) -相关文章
*   HTML 解析器-轻松提取 HTML 信息 -一些实用的代码片段来提取和处理 HTML 信息

* * *

## 其他解析资源

*   [HTML 解析器——如何用 Python BS4 少干活](https://blog.appseed.us/html-parser-python-beautifulsoup-use-cases/)
*   [HTML 解析器](https://blog.appseed.us/appseed-app-generator-html-parsing/) -由 AppSeed 应用程序生成器用来解析平面 HTML
*   [BeautifulSoup Html 解析器](https://www.crummy.com/software/BeautifulSoup)文档
*   [HTML 解析源](https://github.com/app-generator/html-parser) -官方公共库
*   由 AppSeed 提供的 HTML 解析器
*   HTML 解析器-将 HTML 转换成 Jinja2 和 Php 组件 -相关博客文章
*   视频演示 [HTML 解析](https://www.youtube.com/watch?v=N8l1tTurKP0)和组件提取

* * *

> 有用吗？AMA 在评论中。谢谢&开心 **HTML 解析**！