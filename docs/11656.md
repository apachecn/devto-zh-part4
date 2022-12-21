# 用蛇(Python)翻译

> 原文：<https://dev.to/petercour/translate-with-the-snake-4i04>

有一些网站提供语言翻译。你可能知道谷歌翻译就是其中之一。

你知道你可以从 [Python](https://pythonprogramminglanguage.com/) 代码中使用这些网站吗？欢迎来到丛林！

[![](img/98515e20cb2c23e5761dd48002111dcd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GjoRqa9f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252FzC4YhpDY76OxW%252Fgiphy.gif%26f%3D1)

为此，您需要模块 requests 和 BeautifulSoup。这些模块用于发出 web 请求和获取数据。下面的示例代码将一个字符串从英语翻译成法语。

```
#!/usr/bin/python3
import requests
from bs4 import BeautifulSoup

def getHTMLText(url):
    try:
        r = requests.get(url, timeout=30)
        r.raise_for_status()
        return r.text
    except:
        print("Get HTML Failed!")
        return 0

def google_translate(to_translate, from_language="en", to_language="fr"):
    base_url = "https://translate.google.com/m?hl={}&sl={}&ie=UTF-8&q={}"
    url = base_url.format(to_language, from_language, to_translate)

    html = getHTMLText(url)
    if html:
        soup = BeautifulSoup(html, "html.parser")

    try:
        result = soup.find_all("div", {"class":"t0"})[0].text
    except:
        print("Translation Failed!")
        result = ""

    return result

if __name__ == '__main__':
    print(google_translate("Hello World!")) 
```

这里最重要的部分是最后一行，这是一个对 google_translate 的函数调用，它把要翻译的文本作为参数。

如果您运行它:

```
python3 example.py
Bonjour le monde! 
```

相关链接:

*   [BeautifulSoup 模块，网页抓取](https://pypi.org/project/beautifulsoup4/)
*   [学习 Python 编程](https://pythonprogramminglanguage.com/)
*   [语音识别，文本到语音转换](https://pythonprogramminglanguage.com/speech-recognition/)