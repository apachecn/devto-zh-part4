# 在 Netlify 和 Jekyll 中使用重定向

> 原文：<https://dev.to/khansubhan95/using-redirects-with-netlify-and-jekyll-4ohp>

我的[博客](https://mskhan.in)是用 Jekyll 制作的，托管在 Netlify 上。我很惊讶地得知 Netlify 支持重定向。下面是如何为 Jekyll(或任何静态生成器)配置它的方法

### 第一步:在项目根目录下创建一个 _redirects 文件

在项目根目录下创建一个 _redirects 文件。填充它重定向 url 和您希望它们指向的 URL。例如，我想指出/恢复到/关于

```
/resume /about 
```

您也可以使用#添加注释

### 第二步:在 _config.yml 中包含 _ 重定向

这是哲基尔特有的步骤。您需要在 config.yml 中包含 _redirects。在 config.yml 中添加一个 includes 条目，如 so

```
include: [_redirects] 
```

### 第三步:部署到网络生活

将您的站点部署到 Netlify，您就大功告成了。如果您输入`<your_base_url>/resume`，您将被重定向至`<your_base_url>/about`。