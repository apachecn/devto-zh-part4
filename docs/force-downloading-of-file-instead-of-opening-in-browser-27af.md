# 强制下载文件，而不是在浏览器中打开

> 原文：<https://dev.to/jahboo/force-downloading-of-file-instead-of-opening-in-browser-27af>

当您浏览该链接时，一些文件将在浏览器中打开。这种行为对于某些内容类型(例如，图像、pdf 等)来说是典型的。)

但是，您可以强制下载文件，而不是在用户点击链接时打开文件。

### 第 1 路(前端):

HTML 属性*下载*允许你这样做。

```
<a href="/public/report.pdf" download="stat_report"> 
```

Enter fullscreen mode Exit fullscreen mode

如果属性的值被省略，将使用原始文件名。然而，要小心。一些不支持 HTML5 的旧浏览器不支持该属性。
如果给定文件存储在另一台主机上，重命名不起作用。

### 第二路(后端):

可以设置 HTTP 头`Content-disposition`。

for Nginx:

```
location ~* /public/(.+\.pdf)$ {
    add_header Content-disposition "attachment; filename=$1";
} 
```

Enter fullscreen mode Exit fullscreen mode

对于阿帕奇:

```
<IfModule mod_headers.c>
    <FilesMatch "\.(?i:pdf)$">
        ForceType application/octet-stream
        Header set Content-Disposition "attachment"
    </FilesMatch>
</IfModule> 
```

Enter fullscreen mode Exit fullscreen mode