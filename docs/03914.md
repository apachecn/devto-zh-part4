# 我的自动化任务集合

> 原文：<https://dev.to/endormi/collection-of-my-automated-tasks-3mgd>

我创建了一个用 python 编写的自动化任务集合。

在 GitHub 查看:[https://github.com/endormi/automation](https://github.com/endormi/automation)
首发不胜感激😃

我已经创建了文件管理器，加密货币通知，文件重命名等任务。

文件管理器:

根据文件类型
将文件移动到其首选位置

```
for mv in move_file:
    if mv.endswith(('.png', '.jpg', '.jpeg', '.gif', '.ico', '.svg')):
        shutil.move(mv, images)
    if mv.endswith(('.mp3', '.wav', '.mid', '.midi', '.ogg')):
        shutil.move(mv, audio)
    if mv.endswith(('.pdf', '.doc', '.docx', '.docxml')):
        shutil.move(mv, docs)
    if mv.endswith(('.txt', '.text', '.note', '.rtf')):
        shutil.move(mv, texts)
    if mv.endswith(('.mp4', '.avi', '.mov', '.vmw', '.flv', '.mpeg', '.mpg')):
        shutil.move(mv, videos)
    if mv.endswith(('.zip', '.rar', '.tar', '.7z', '.pkg')):
        shutil.move(mv, compressed_files) 
```

Enter fullscreen mode Exit fullscreen mode

加密货币通知程序:

当 LiteCoin 价格变化时发送通知(使用 ifttt 应用程序构建)
是的，它适用于您希望使用的任何加密货币。

```
while True:
        price = latest_price()
        date = datetime.now()
        lc.append({'date': date, 'price': price})

        if len(lc) == 3:
            ifttt_hook('applet_name', format_lc(lc))

        lc = []

        time.sleep(500) 
```

Enter fullscreen mode Exit fullscreen mode

文件重命名器:

我只有一个标题和编号，但把它改成你的文件的格式。

```
for files in os.listdir():
    file, extension = os.path.splitext(files)
    title, num = file.split('-')
    title = title.strip()
    num = num.strip()[0:].zfill(3)

    rename_file = '{}-{}{}'.format(num, title, extension) 
```

Enter fullscreen mode Exit fullscreen mode

非常感谢您的反馈！