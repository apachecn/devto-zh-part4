# 网络爬虫 Python 如何用 Python 从头开始创建网络爬虫

> 原文：<https://dev.to/fprime/how-to-create-a-web-crawler-from-scratch-in-python-2p46>

# 概述

大多数 Python web 爬行/抓取教程都使用某种爬行库。如果你想快速完成工作，这是很好的，但是如果你不明白刮擦是如何工作的，那么当问题出现时，就很难知道如何解决它们。

在本教程中，我将讲述如何只用 Python 标准库和请求模块([https://pypi.org/project/requests/2.7.0/](https://pypi.org/project/requests/2.7.0/))用 Python 完全从头开始编写一个网络爬虫。我还将介绍如何使用代理 API(【https://proxyorbit.com】T2)来防止你的爬虫被列入黑名单。

这主要是为了教育目的，但是只要稍加注意和小心，这个爬虫就可以变得像任何使用库编写的 scraper 一样健壮和有用。不仅如此，它很可能会更轻，更便于携带。

我假设你对 Python 和编程有基本的了解。要完全理解代码，需要理解 HTTP 请求和正则表达式的工作原理。我不会深入讨论每个单独功能的实现细节。相反，我将给出代码示例如何工作的高层次概述，以及为什么某些事情以它们的方式工作。

我们将在本教程中制作的爬虫的目标是“索引互联网”,类似于谷歌爬虫的工作方式。显然，我们将无法索引互联网，但这个想法是，这个爬虫将遵循整个互联网的链接，并保存这些链接在某个地方以及页面上的一些信息。

# 从小做起

第一项任务是为我们的铲运机打基础。我们将使用一个类来存放我们所有的函数。我们还需要 re 和 requests 模块，因此我们将导入它们

```
import requests    
import re    

class PyCrawler(object):    
    def __init__(self, starting_url):    
        self.starting_url = starting_url                       
        self.visited = set()    

    def start(self):    
        pass                 

if __name__ == "__main__":    
    crawler = PyCrawler()    
    crawler.start() 
```

Enter fullscreen mode Exit fullscreen mode

你可以看到这是非常简单的开始。重要的是逐步建立这些东西。一点点编码，一点点测试等等。

我们有两个实例变量，将有助于我们以后的爬行工作。

`starting_url`

是我们的爬虫将开始的最初的开始 URL

`visited`

这将使我们能够跟踪我们目前访问过的网址，以防止访问同一个网址两次。使用 set()可以在 O(1)时间内查找访问过的 URL，速度非常快。

# 抓取网站

现在我们将开始实际编写爬虫。下面的代码将向 starting_url 发出请求，并提取页面上的所有链接。然后，它将迭代所有新链接，并从新页面收集新链接。它将继续这个递归过程，直到从起点刮去所有可能的链接。有些网站不链接外部，所以这些网站会比链接到其他网站的网站更快停止。

```
import requests    
import re    
from urllib.parse import urlparse    

class PyCrawler(object):    
    def __init__(self, starting_url):    
        self.starting_url = starting_url    
        self.visited = set()    

    def get_html(self, url):    
        try:    
            html = requests.get(url)    
        except Exception as e:    
            print(e)    
            return ""    
        return html.content.decode('latin-1')    

    def get_links(self, url):    
        html = self.get_html(url)    
        parsed = urlparse(url)    
        base = f"{parsed.scheme}://{parsed.netloc}"    
        links = re.findall('''<a\s+(?:[^>]*?\s+)?href="([^"]*)"''', html)    
        for i, link in enumerate(links):    
            if not urlparse(link).netloc:    
                link_with_base = base + link    
                links[i] = link_with_base       

        return set(filter(lambda x: 'mailto' not in x, links))    

    def extract_info(self, url):                                
        html = self.get_html(url)                               
        return None                  

    def crawl(self, url):                   
        for link in self.get_links(url):    
            if link in self.visited:        
                continue                    
            print(link)                 
            self.visited.add(link)            
            info = self.extract_info(link)    
            self.crawl(link)                  

    def start(self):                     
        self.crawl(self.starting_url)    

if __name__ == "__main__":                           
    crawler = PyCrawler("https://google.com")        
    crawler.start() 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，已经添加了相当多的新代码。

首先，添加了 get_html、get_links、crawl 和 extract_info 方法。

`get_html()`

用于获取当前链接处的 HTML

`get_links()`

从当前页面提取链接

`extract_info()`

将用于提取页面上的特定信息。

还添加了`crawl()`函数，这可能是这段代码中最重要也是最复杂的部分。“爬行”递归工作。它从 start_url 开始，从页面中提取链接，遍历这些链接，然后递归地将链接反馈给自己。

如果你把网络想象成一系列的门和房间，那么本质上这段代码所做的就是寻找这些门，并穿过它们，直到到达一个没有门的房间。当这种情况发生时，它会回到一个有未被探索的门的房间，并进入那扇门。它会一直这样做，直到从起始位置可进入的所有门都被进入。这种过程非常适合递归代码。

如果你现在运行这个脚本，它将会搜索并打印所有从 google.com 开始的新网址

# 摘录内容

现在我们将从页面中提取数据。这个方法(extract_info)很大程度上是基于你想用你的刮刀做什么。对于本教程来说，我们要做的就是提取 meta 标签信息，如果我们能在页面上找到它的话。

```
import requests    
import re    
from urllib.parse import urlparse    

class PyCrawler(object):    
    def __init__(self, starting_url):    
        self.starting_url = starting_url    
        self.visited = set()    

    def get_html(self, url):    
        try:    
            html = requests.get(url)    
        except Exception as e:    
            print(e)    
            return ""    
        return html.content.decode('latin-1')    

    def get_links(self, url):    
        html = self.get_html(url)    
        parsed = urlparse(url)    
        base = f"{parsed.scheme}://{parsed.netloc}"    
        links = re.findall('''<a\s+(?:[^>]*?\s+)?href="([^"]*)"''', html)    
        for i, link in enumerate(links):    
            if not urlparse(link).netloc:    
                link_with_base = base + link    
                links[i] = link_with_base    

        return set(filter(lambda x: 'mailto' not in x, links))    

    def extract_info(self, url):    
        html = self.get_html(url)    
        meta = re.findall("<meta .*?name=[\"'](.*?)['\"].*?content=[\"'](.*?)['\"].*?>", html)    
        return dict(meta)    

    def crawl(self, url):    
        for link in self.get_links(url):    
            if link in self.visited:    
                continue    
            self.visited.add(link)    
            info = self.extract_info(link)    

            print(f"""Link: {link}    
Description: {info.get('description')}    
Keywords: {info.get('keywords')}    
            """)    

            self.crawl(link)    

    def start(self):    
        self.crawl(self.starting_url)    

if __name__ == "__main__":    
    crawler = PyCrawler("https://google.com")     
    crawler.start() 
```

Enter fullscreen mode Exit fullscreen mode

除了新的打印格式和 extract_info 方法之外，这里没有什么变化。

这里的神奇之处在于 extract_info 方法中的正则表达式。它在 HTML 中搜索所有遵循格式`<meta name=X content=Y>`的 meta 标签，并返回格式为{X:Y}的 Python 字典

然后，这些信息会被打印到每个请求的每个 URL 的屏幕上。

# 集成旋转代理 API

网络爬行和网络抓取的一个主要问题是，如果你提出太多的请求，不使用可接受的用户代理等，网站将禁止你。限制这种情况的方法之一是使用代理，并为 crawler 设置不同的用户代理。通常情况下，代理方法需要你出去购买或从其他地方手动获取代理列表。很多时候，这些代理甚至不工作，或者非常慢，使得网络爬行更加困难。

为了避免这个问题，我们将使用所谓的“旋转代理 API”。旋转代理 API 是一个负责为我们管理代理的 API。我们所要做的就是向他们的 API 端点发出请求，然后嘣，我们将为我们的爬虫获得一个新的工作代理。将服务集成到平台中只需要几行额外的 Python 代码。

我们将使用的服务是代理轨道([https://proxyorbit.com](https://proxyorbit.com))。完全公开，我拥有和运行代理轨道。

该服务专门为 web 爬行应用程序创建代理解决方案。不断检查代理，以确保池中只有最佳的工作代理。

```
import requests    
import re    
from urllib.parse import urlparse    
import os    

class PyCrawler(object):    
    def __init__(self, starting_url):    
        self.starting_url = starting_url    
        self.visited = set()    
        self.proxy_orbit_key = os.getenv("PROXY_ORBIT_TOKEN")    
        self.user_agent = "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.103 Safari/537.36"    
        self.proxy_orbit_url = f"https://api.proxyorbit.com/v1/?token={self.proxy_orbit_key}&ssl=true&rtt=0.3&protocols=http&lastChecked=30"    

    def get_html(self, url):                                                                                                               
        try:                                                                                                                               
            proxy_info = requests.get(self.proxy_orbit_url).json()                                                                         
            proxy = proxy_info['curl']                                                                                                
            html = requests.get(url, headers={"User-Agent":self.user_agent}, proxies={"http":proxy, "https":proxy}, timeout=5)        
        except Exception as e:                                                                                                        
            print(e)                                                                                                                  
            return ""                                                                                                                 
        return html.content.decode('latin-1')                                                                                         

    def get_links(self, url):    
        html = self.get_html(url)    
        parsed = urlparse(url)    
        base = f"{parsed.scheme}://{parsed.netloc}"    
        links = re.findall('''<a\s+(?:[^>]*?\s+)?href="([^"]*)"''', html)    
        for i, link in enumerate(links):    
            if not urlparse(link).netloc:    
                link_with_base = base + link    
                links[i] = link_with_base    

        return set(filter(lambda x: 'mailto' not in x, links))    

    def extract_info(self, url):    
        html = self.get_html(url)    
        meta = re.findall("<meta .*?name=[\"'](.*?)['\"].*?content=[\"'](.*?)['\"].*?>", html)    
        return dict(meta)    

    def crawl(self, url):    
        for link in self.get_links(url):    
            if link in self.visited:    
                continue    
            self.visited.add(link)    
            info = self.extract_info(link)    

            print(f"""Link: {link}    
Description: {info.get('description')}    
Keywords: {info.get('keywords')}    
            """)    

            self.crawl(link)    

    def start(self):    
        self.crawl(self.starting_url)    

if __name__ == "__main__":    
    crawler = PyCrawler("https://google.com")    
    crawler.start() 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，这里并没有太大的变化。创建了三个新的类变量:`proxy_orbit_key`、`user_agent`和`proxy_orbit_url`

proxy_orbit_key 从名为`PROXY_ORBIT_TOKEN`的环境变量中获取代理轨道 API 令牌

user_agent 将爬虫的用户代理设置为 Firefox，使请求看起来像来自浏览器

`proxy_orbit_url`是我们将触及的代理 Orbit API 端点。我们将只过滤在过去 30 分钟内检查过的请求支持 SSL 的 HTTP 代理的结果。

在 get_html 中，对代理 Orbit API URL 发出了一个新的 HTTP 请求，以获取随机代理，并将其插入到请求模块中，以获取我们试图从代理后面抓取的 URL。

如果一切顺利，那就是了！我们现在应该有一个真正的工作网络爬虫，它从网页中提取数据并支持旋转代理。

更新:

似乎有些人在最终剧本上遇到了麻烦，具体来说就是`get_html`方法。这可能是因为缺少设置的代理 Orbit API 令牌。在构造函数中有一行，`self.proxy_orbit_key = os.getenv("PROXY_ORBIT_TOKEN")`。这一行试图获取一个名为`PROXY_ORBIT_TOKEN`的环境变量，这是应该设置 API 令牌的地方。如果没有设置令牌，行`proxy = proxy_info['curl']`将失败，因为代理 API 将返回 JSON，表示一个未经验证的请求，并且不包含任何键`curl`。

有两种方法可以解决这个问题。第一个是在 Proxy Orbit 注册，获得您的令牌并正确设置您的`PROXY_ORBIT_TOKEN` env 变量。第二种方法是将你的`get_html`函数替换成下面的:

```
 def get_html(self, url):                                                                                                               
        try:                                                                                                                                                                                                                            
            html = requests.get(url, headers={"User-Agent":self.user_agent}, timeout=5)        
        except Exception as e:                                                                                                        
            print(e)                                                                                                                  
            return ""                                                                                                                 
        return html.content.decode('latin-1') 
```

Enter fullscreen mode Exit fullscreen mode

请注意，如果您替换 get_html 函数，您的所有爬虫请求都将使用您的 IP 地址。