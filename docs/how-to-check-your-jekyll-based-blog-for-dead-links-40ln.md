# 如何:检查基于 Jekyll 的博客中的死链接

> 原文：<https://dev.to/seankilleen/how-to-check-your-jekyll-based-blog-for-dead-links-40ln>

我想有人可能会发现这个快速提示有用，所以我把它写下来。

## 挑战

我有一个博客，现在有相当数量的帖子，其中一些帖子已经有 2012 年了。

我担心有一些死链接。

## 解决方案:使用 html-proofer gem 和 RakeFile

*   打开您博客的`Gemfile`
*   将`gem 'html-proofer'`添加到文件中
*   创建一个`RakeFile`

现在您将在您的软件包安装中下拉`html-proofer`。那么，我们如何让它实际进行安装呢？

修改你的 Rakefile，添加一些类似的东西(我现在的这个[可以在这里找到](https://github.com/seankilleen/seankilleen.github.io/blob/master/RakeFile) ):

```
require 'html-proofer' # Ensures we have the html-proofer library available to use

def run_htmlproofer() # The function that will run the proofer, so that we can re-use it between our two rake tasks
  options = { 
    assume_extension: true, # Assumes html file extensions
    :typhoeus => { # The options for the curl library that's used.
      :ssl_verifypeer => false # This will stop you from getting errors when certs can't be parsed, which doesn't matter in this case.
    }, 
    allow_hash_href: true, # Won't fail for local links 
    url_ignore: [/edit\/gh-pages/] # This is because all my pages have a link to edit them, which will fail when generated locally.
  }
  HTMLProofer.check_directory("./_site", options).run # Calls html-proofer and uses the Jektll _site folder
end

task :test do
  sh "bundle exec jekyll build"
  run_htmlproofer()
end

task :testwithoutbuild do # For when I just built the site and I'm doing this a bunch of times
  run_htmlproofer()
end 
```

Enter fullscreen mode Exit fullscreen mode

## 如何使用

一旦你准备好了`Rakefile`，你应该能够进入那个目录并运行`rake test`或`rake testwithoutbuild`，它们将解析你的链接并帮助你。

我刚刚运行了它，并最终更新了 20 多个链接，所以这绝对是一个伟大的检查！

## 就是这样！

有问题吗？问题？请在评论中告诉我。

编码快乐！