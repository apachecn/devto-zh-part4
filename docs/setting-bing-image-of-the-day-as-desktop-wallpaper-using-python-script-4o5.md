# 使用 Python 脚本将 bing 每日图片设置为桌面壁纸

> 原文：<https://dev.to/anuragrana/setting-bing-image-of-the-day-as-desktop-wallpaper-using-python-script-4o5>

最初发表于

 **使用这个 python 脚本，我们将尝试下载当天的 bing 图像，并将其设置为桌面壁纸。

bing 本身有一个 URL，它使用 AJAX 获取图像细节。我们将向该 URL 发送 GET 请求以获取图像细节。

```
# get image url response = requests.get("https://www.bing.com/HPImageArchive.aspx?format=js&idx=0&n=1&mkt=en-US")
image_data = json.loads(response.text) 
```

返回的响应将采用以下格式。

```
{
    "images": [{
        "startdate": "20190823",
        "fullstartdate": "201908230700",
        "enddate": "20190824",
        "url": "/th?id=OHR.FarmlandLandscape_EN-US6661316442_1920x1080.jpg&rf=LaDigue_1920x1080.jpg&pid=hp",
        "urlbase": "/th?id=OHR.FarmlandLandscape_EN-US6661316442",
        "copyright": "Farmland in Washington state's Palouse region (© Art Wolfe/Getty Images)",
        "copyrightlink": "https://www.bing.com/search?q=palouse+region&form=hpcapt&filters=HpDate%3a%2220190823_0700%22",
        "title": "Harvest time in the Palouse",
        "quiz": "/search?q=Bing+homepage+quiz&filters=WQOskey:%22HPQuiz_20190823_FarmlandLandscape%22&FORM=HPQUIZ",
        "wp": true,
        "hsh": "44238c657b35ce3c11fbf3f59881474e",
        "drk": 1,
        "top": 1,
        "bot": 1,
        "hs": []
    }],
    "tooltips": {
        "loading": "Loading...",
        "previous": "Previous image",
        "next": "Next image",
        "walle": "This image is not available to download as wallpaper.",
        "walls": "Download this image. Use of this image is restricted to wallpaper only."
    }
} 
```

从响应 JSON 中获取图像 URL 并下载它。

```
# download and save image img_data = requests.get(full_image_url).content
with open(image_name, 'wb') as handler:
    handler.write(img_data) 
```

图像将保存在当前工作目录中。使用下面的命令将此图像设置为桌面背景。

```
`which gsettings` set org.gnome.desktop.background picture-uri file:$PWD/imagename 
```

这个命令适用于 Ubuntu。对于其他操作系统，请相应更新。

该脚本可在 Github 上获得。

您可以通过安排该脚本在系统启动时运行来自动化该过程。

最初发表于**