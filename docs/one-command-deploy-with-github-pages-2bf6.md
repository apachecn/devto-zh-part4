# 使用 GitHub 页面部署一个命令

> 原文：<https://dev.to/nervewax/one-command-deploy-with-github-pages-2bf6>

<small>*封面形象由雄壮的[安德烈·沃林](https://andreewallin.com/)*T5】</small>

GitHub Pages 的一个警告是，如果你没有使用 GitHub Pages Jekyll gem，它不会自动构建你的站点。最重要的是，你不能使用任何 GitHub Pages 不支持[的 Jekyll 插件。](https://pages.github.com/versions/)

也就是说，GitHub 会为你部署*编译好的*代码，要么从`/docs`文件夹，一个`gh-pages`分支，要么从`master`分支。[【ref】](https://help.github.com/en/articles/configuring-a-publishing-source-for-github-pages)

我现在使用的解决方案仍然意味着我能够在本地运行我的构建过程，并使用一个命令自动部署。

为此，我为我的网站建立了两个分支:

*   `master`包含我的 Jekyll 编译的`_site/`文件夹，GitHub 会在每次推送时部署它。
    *   应该添加到这个分支，这样 GitHub 就不会试图编译它。
*   包含完整的 repo，带有 Jekyll 源文件。
    *   这种方法适用于任何静态站点生成器。
    *   `_site/`应加到。gitignore 在这个分支上，这样你就不会在那里提交修改。

我已经编写了一个简单的 [npm shell 脚本](https://nervewax.com/npm-scripts-compiler/)，它与`npm deploy`一起运行，本质上是以下命令串的别名:

```
bundle exec jekyll build &&
cd _site/ &&
git add . &&
timestamp=$(date \"+%s\") &&
git commit -m \"automated deployment $timestamp\" &&
git push origin master &&
cd .. 
```

每次我想部署时:

1.  `jekyll build`命令在`_site/`文件夹中编译并构建我的静态站点。
2.  然后我们将目录更改为`_site/`，它有一个. git 配置，设置为 repo 的`master`分支。
3.  然后，脚本提交所有更改(+时间戳)，推送 master 并返回到我的源文件夹。

就是这样！所以现在，一旦我更改了我的站点或内容，我仍然可以用一个命令来构建和部署我的站点。

### 进一步阅读

[从 Ghost CMS 转移到 Jekyll](https://nervewax.com/ghost-to-jekyll/)
[GitHub Pages 基础知识](https://help.github.com/en/categories/github-pages-basics)
[Jekyll 官方文档](https://jekyllrb.com/docs/)