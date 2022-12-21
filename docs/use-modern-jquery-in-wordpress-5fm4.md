# 在 Wordpress 中使用现代 jQuery

> 原文：<https://dev.to/sebtoombs/use-modern-jquery-in-wordpress-5fm4>

## 用现代版替换 Wordpress 中的 jQuery

你知道大多数 Wordpress 站点上使用的 jQuery 的核心版本是 1.12.4(在撰写本文时)吗？！jQuery 的 1.x 分支是在很久以前发布的(在撰写本文时，最新的版本已经发布了 3 年多)。此外，1.x 分支还有一些已知的安全漏洞。

如果你的站点没有非常老的主题或者插件，使用一个更现代的 jQuery 版本可能是个好主意。目前分支最高为 3.x。

许多测试工具，包括 Lighthouse 也会将 jQuery 1.12.5 标记为安全漏洞。

#### **警告:从 jQuery 1.x 升级到 3.x 可能会破坏一些主题/插件**

jQuery 的 2.x 和 3.x 分支从 v1 引入了突破性的变化。如果你有依赖于 1.x 特定功能的旧插件或主题，这可能会破坏你的网站。你被警告了！

#### Wordpress 为什么包含 jQuery 1.12.4？

Wordpress 包含旧版本 jQuery 的主要原因似乎是为了向后兼容主题和插件。jQuery v1 还包括向后兼容 ie6/7/8 等非常老的浏览器的特性。如果您的站点有包含这些旧浏览器的浏览器支持目标，您可能希望继续使用 jQuery 1.12.4。

## 如何替换 Wordpress 中的 jQuery 1.12.4？

好消息是，在您的站点中使用现代版本的 jQuery 非常容易。在撰写本文时，jQuery 的当前版本是 3.4.1。

有两种方法可以在您的前端提供 jQuery 使用提供的 CDN 版本，或者下载到您的主题并在本地提供。在大多数情况下，推荐的方法是在本地提供服务。

#### 使用来自 CDN 的 jQuery

要从 CDN 使用 jQuery，请将这个代码片段添加到您的函数文件中。(或者，如果您不想编辑函数文件，[代码片段插件](https://en-au.wordpress.org/plugins/code-snippets/)是添加额外代码的一种非常简单的方法。另外，看看我们的[顶级 wordpress 插件列表](https://nimblewebdeveloper.com/blog/top-10-wordpress-plugins/)，其中包括代码片段！)

可以在这里找到 jQuery 的最新 CDN 版本: [jQuery CDN](https://code.jquery.com/) 。我们选择了**最新的，缩小的**版本。缩小版最适合生产使用，因为它具有最小的文件大小。

```
<?php  

//Modern Jquery 

add\_action('wp\_enqueue\_scripts', 'nwd\_modern\_jquery');  

function nwd\_modern\_jquery() {  
    global $wp\_scripts;  
    $wp\_scripts->registered['jquery-core']->src = 'https://code.jquery.com/jquery-3.4.1.min.js';  
    $wp\_scripts->registered['jquery']->deps = ['jquery-core'];  
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 本地使用现代 jQuery

首选方案是从您的站点提供一个现代的 jQuery 版本。这可以最大限度地减少对不同来源的请求，从而加快网站的交付速度。如果你实现了一个 CDN，它还允许你从你自己的 CDN 提供 jQuery。

为此，您需要文件/ftp 访问权限。

##### 1。将现代 jQuery 下载到您的主题中

前往 jQuery 站点: [jQuery 下载](https://jquery.com/download/)

下载**压缩文件，制作 jQuery**

你可以把这个文件放在你喜欢的主题的任何地方。我通常建议在你的主题中为第三方库创建一个名为 **vendor** 的文件夹

**/WP-内容/主题//vendor/jquery-3 . 4 . 1 . min . js**

##### 2。将现代 jQuery 从您的主题中排队

将以下代码片段添加到您的**functions.php**中，或者使用代码片段插件添加到代码片段中:

```
<?php  

//Modern Jquery 
add\_action('wp\_enqueue\_scripts', 'nwd\_modern\_jquery');  
function nwd\_modern\_jquery() {  
    global $wp\_scripts;  
    $wp\_scripts->registered['jquery-core']->src = get\_stylesheet\_directory\_uri() .'/vendor/jquery-3.4.1.min.js';  
    $wp\_scripts->registered['jquery']->deps = ['jquery-core'];  
} 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**如果你有 jQuery 到 3.4.1 的不同版本，或者你下载了 jQuery 到你的主题中的不同位置，你就需要修改这一行；

```
<?php  

$wp\_scripts->registered['jquery-core']->src = get\_stylesheet\_directory\_uri() .'/vendor/jquery-3.4.1.min.js'; 
```

Enter fullscreen mode Exit fullscreen mode

以反映正确的文件位置和名称。

#### 测试！

现在，您的站点前端应该有一个现代版本的 jQuery 了。恭喜你！不要忘记测试它——浏览到站点的前端，寻找包含 jQuery 的标签。你应该不再有 Wordpress 核心的版本，应该有你刚刚添加的版本。另外，打开您的开发工具并浏览站点，查找 javascript 错误。很多插件和主题写的很差！很可能会有问题。