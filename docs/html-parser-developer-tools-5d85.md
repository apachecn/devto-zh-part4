# HTML 解析器开发工具

> 原文：<https://dev.to/sm0ke/html-parser-developer-tools-5d85>

各位编码员好，

在本文中，我将展示一个简单的 **[HTML 解析器](https://appseed.us/developer-tools/html-parser)** ，我用它将更快的 HTML 主题集成到用不同技术编码的遗留应用程序中。当客户为他的应用程序请求一个新的 UI 时，手动处理可能需要一些时间，我决定对整个流程进行一点自动化。使用这个工具，我可以在不到 2 小时的时间内更新一个 2/3 页面的简单网站的设计。

> *感谢阅读！* -内容由 **[App 生成器](https://appseed.us/)** 提供。

* * *

## 主要特征

该工具将平面 HTML 转换成可用于不同引擎的现成组件: [PUG](https://pugjs.org/api/getting-started.html) 、 [Jinja2](http://jinja.pocoo.org/docs/2.10/) 、 [Blade](https://laravel.com/docs/5.8/blade) 、 [Mustache](https://laravel.com/docs/5.8/blade) 、Core Php。

* * *

> 注意:该工具不是开源的，但我会考虑在未来发布一个轻量级版本作为开源项目。在 [HTML 解析器公共存储库](https://github.com/app-generator/html-parser)中，我将发布转换成 PUG、Jinja 和 Blade 的经过处理的 HTML 主题，供任何人使用。

* * *

## 技术

[HTML 解析器](https://docs.python.org/3/library/html.parser.html)工具在[python 3](https://www.python.org/)/[beautiful soup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)库中开发，作为交互控制台。在 3 个月的研发工作之后，我能够在实际项目中使用这个工具。

* * *

## [HTML 解析器](https://appseed.us/developer-tools/html-parser)功能

*   规范化 HTML 文件以从标准目录(/assets/ [ img，js，css ])加载资产，从而使 webpack 相关工具中的集成更加容易
*   编辑/遍历 HTML 树
*   编辑属性，如锚 HREF，跨文本，删除元素，编辑类名
*   提取部件用于各种发动机的生产，如帕格、金贾 2、叶片
*   将遗留引导布局迁移到布尔玛和顺风 CSS 框架

* * *

## [HTML 解析器](https://appseed.us/developer-tools/html-parser)实现

为了处理 HTML 和 HTML 树，我们需要首先加载整个文件。 [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/) 有一个简单的构造函数，它接受要解析的字符串并加载到内存和所需的解析器中。

### 在内存中加载 HTML

```
# read_file retun the file content as string html_content = read_file('index.html')
soup  = bs(html_content,'html.parser') 
# At this point, we can interact with the HTML 
# elements stored in memory using all helpers offered by BS library 
```

Enter fullscreen mode Exit fullscreen mode

BeautifulSoup 库支持不止一个解析器(例如 lxml、xml、html5lib)，它们之间的差异在非标准格式的 html 文档中变得很明显。例如，lxml 将为所有元素添加缺少的结束标记。欲了解更多信息，请访问本主题相关文档中的[专用章节](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#differences-between-parsers)。

### 解析头节

要选择整个头部节点，并与所有元素进行交互，我们只需编写几行代码:

```
header = soup.find('head')

# If we want to change the title 
header.title.string.replace_with('My new title') 
```

Enter fullscreen mode Exit fullscreen mode

### 解析 JS 脚本的 HTML

Javascript 文件使用`script`节点呈现在 HTML 中:

```
...
<script type='text/javascript' src='js/bootstrap.js'></script>
<script type='text/javascript' src='js/custom.js'></script>
... 
```

Enter fullscreen mode Exit fullscreen mode

为了扫描 HTML soup 中的脚本标签，我们可以使用`find_all`助手:

```
for script in soup.body.find_all('script', recursive=False):

   # Print the path 
   print(' JS source = ' + script[src]) 

   # Update (normalize) the path
   js_path = script['src']
   js_file = js_path.split('/')[-1] # select the last segment
   script[src] = '/assets/js/' + js_file 
```

Enter fullscreen mode Exit fullscreen mode

### 解析图片的 HTML

使用与 JS 文件相同的技术，我们可以规范化从标准目录加载的图像。

```
for img in soup.body.find_all('img'):

   # Print the path 
   print(' IMG src = ' + img[src]) 

   img_path = img['src']
   img_file = img_path.split('/')[-1]  
   img[src] = '/assets/img/' + img_file 
```

Enter fullscreen mode Exit fullscreen mode

### 保存 HTML

我们所有的改变都是在记忆中产生的。为了使这些改变永久化，我们需要从 BS 中提取我们处理过的 HTML 的字符串表示，并将其转储到一个文件中以备后用:

```
processed_html = soup.prettify(formatter="html")
f = open( 'index2.html', 'w+')
f.write(processed_html)
f.close 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 真实的生活样本

这个示例，摘自 [Stellar HTML5Up 主题](https://github.com/app-generator/html-parser/tree/master/sample-stellar)是一个简单的导航栏，摘自[这个文件](https://github.com/app-generator/html-parser/blob/master/sample-stellar/index.html)

*   索引文件:[原始版本](https://github.com/app-generator/html-parser/blob/master/sample-stellar/index.html)和[规范化版本](https://github.com/app-generator/html-parser/blob/master/sample-stellar/index2.html)
*   [JSON 描述符](https://github.com/app-generator/html-parser/blob/master/sample-stellar/index2.json)由 **HTML 解析器**工具生成，封装 HTML 文件使用的资产和资源
*   导航组件
    *   [HTML 版本](https://github.com/app-generator/html-parser/blob/master/sample-stellar/components/nav.html)
    *   [帕格版](https://github.com/app-generator/html-parser/blob/master/sample-stellar/components/nav.pug)
    *   [Jinja2 版本](https://github.com/app-generator/html-parser/blob/master/sample-stellar/components/nav.j2)
    *   [Php 版本](https://github.com/app-generator/html-parser/blob/master/sample-stellar/components/nav.php)
    *   [JSON 描述符](https://github.com/app-generator/html-parser/blob/master/sample-stellar/components/nav.json)

### 帕格版

```
nav#nav
  ul
    li
      a.active.newclass(href='https://appseed.us/html-parser').

        Introduction

    li
      a(href='#first').

        First Section

    li
      a(href='#second').

        Second Section

    li
      a(href='#cta').

        Get Started 
```

Enter fullscreen mode Exit fullscreen mode

### PHP 版本

```
<nav id="nav">
 <ul>
  <li>
   <a class="active newclass" href="https://appseed.us/html-parser">
    <?php echo $var_1?>
   </a>
  </li>
  <li>
   <a href="#first">
    <?php echo $var_2?>
   </a>
  </li>
  <li>
   <a href="#second">
    <?php echo $var_3?>
   </a>
  </li>
  <li>
   <a href="#cta">
    <?php echo $var_4?>
   </a>
  </li>
 </ul>
</nav> 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 用此工具构建的项目

所有都是开源的，有现场演示。

*   [JAMstack 分形](https://github.com/app-generator/jamstack-fractal) -以 JAMstack 模式编码的 HTML5Up 设计
*   [JAMstack BigPicture](https://github.com/app-generator/jamstack-big-picture) -以 JAMstack 模式编码的 HTML5Up 设计
*   [JAMstack 登陆](https://github.com/app-generator/jamstack-landed) -以 JAMstack 模式编码的 HTML5Up 登陆设计
*   [烧瓶仪表板材料设计](https://github.com/app-generator/flask-material-dashboard) -带材料设计的管理仪表板
*   [烧瓶仪表盘 NowUI](https://github.com/app-generator/flask-now-ui-dashboard) -采用 NowUI 设计的管理仪表盘
*   [烧瓶仪表盘黑色](https://github.com/app-generator/flask-black-dashboard) -开源管理面板
*   烧瓶仪表盘氩气 -开源管理面板
*   烧瓶仪表盘灯 -开源管理面板

* * *

## 资源

*   [HTML 解析器——如何用 Python BS4 少干活](https://blog.appseed.us/html-parser-python-beautifulsoup-use-cases/)
*   [开发者工具-开源 HTML 解析器](https://blog.appseed.us/developer-tools-html-parser/) -相关文章
*   [HTML 解析器](https://blog.appseed.us/appseed-app-generator-html-parsing/) -由 AppSeed 应用程序生成器用来解析平面 HTML
*   [BeautifulSoup Html 解析器](https://www.crummy.com/software/BeautifulSoup)文档
*   [HTML 解析源](https://github.com/app-generator/html-parser) -官方公共库
*   由 AppSeed 提供的 HTML 解析器
*   HTML 解析器-将 HTML 转换成 Jinja2 和 Php 组件 -相关博客文章
*   视频演示 [HTML 解析](https://www.youtube.com/watch?v=N8l1tTurKP0)和组件提取

* * *

> 谢谢大家！