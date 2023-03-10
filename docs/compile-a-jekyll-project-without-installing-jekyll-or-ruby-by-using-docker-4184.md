# 使用 Docker 编译 Jekyll 项目，无需安装 Jekyll 或 Ruby

> 原文：<https://dev.to/michael/compile-a-jekyll-project-without-installing-jekyll-or-ruby-by-using-docker-4184>

我目前运行我的站点的选择是 [Jekyll，一个用 Ruby](https://jekyllrb.com/) 编写的静态站点生成器。自从我在 2013 年抛弃 Wordpress 后，我就一直只使用 Jekyll。与设置 Wordpress 相比，在 Jekyll 上启动并运行要容易得多，但在启动并运行它的过程中仍然有一些障碍。

自 2013 年以来，有无数次我不得不设置一台新电脑，这意味着让 Jekyll 从头开始运行。这通常意味着安装 Homebrew，rbenv，Ruby，然后是 Jekyll，祈祷一切都能第一次运行。更不用说还要配置 git，这样我就可以提交回 Github repo，它包含了站点的所有文件。

上周，我开始重新审视 Docker。Docker 是一种技术，可以让你从一个配置文件中运行容器中的应用程序。这非常强大，因为它允许您避免古老的开发格言“它在我的计算机上工作”。

有了 Docker，我发现我可以在本地编译甚至服务我的 Jekyll 站点，而不需要在本地机器上运行 Jekyll。虽然不需要安装任何 Jekyll 依赖项，但是需要安装 Docker 才能让 Docker 运行。但我发现这真的很简单，因为他们提供了一个应用程序，提供了 [CLI](https://www.spellitout.xyz/cli-command-line-interface/) 和设置。

## 用 Docker 构建你的 Jekyll 项目

[一旦 Docker 安装并设置在您的机器](https://www.docker.com/products/docker-desktop)上，您就可以通过从命令行运行项目文件夹
中的这个命令来构建您的 Jekyll 项目

```
docker run --rm -it --volume="$PWD:/srv/jekyll" --volume="$PWD/vendor/bundle:/usr/local/bundle" --env JEKYLL_ENV=production jekyll/jekyll:3.8 jekyll build 
```

Enter fullscreen mode Exit fullscreen mode

该命令的作用是:

*   `--rm`退出时自动移除容器
*   `--volume="$PWD:/srv/jekyll"`获取由`$PWD`指示的当前目录，并将其映射到容器中位于`/srv/jekyll`的目录，以便它可以构建它
*   `--volume="$PWD/vendor/bundle:/usr/local/bundle"`该选项映射当前目录的`/vendor/bundle`内容，并将其映射到`/usr/local/bundle`。选择这个选项的原因是为了在未来的构建中缓存和重用 gems
*   在我的 Jekyll 项目的不同部分，我已经为它指定了只有在生产时才渲染。例如，分析不应该被我的本地开发搞得一团糟。这将`JEKYLL_ENV`的环境变量设置为`production`
*   `jekyll/jekyll:3.8`这告诉它使用 [Jekyll 容器](https://github.com/envygeeks/jekyll-docker)的`jekyll:3.8`标记版本
*   运行 Jekyll 的构建命令

如果您是第一次运行这个命令，这个容器的映像不会在您的系统上，所以它会在运行容器之前先从 Docker 注册表中获取它。

```
Unable to find image 'jekyll/jekyll:3.8' locally
3.8: Pulling from jekyll/jekyll
e7c96db7181b: Pull complete
622c94c90cb1: Pull complete
5ab26e9d8a17: Pull complete
830997f3d72a: Pull complete
1956a4eaab3f: Pull complete
36a9759f9f2f: Pull complete
Digest: sha256:deb267df3f6c2b7604b0d5a5aabcb394eec1452a053e4297cf2fb13b125e0bcf
Status: Downloaded newer image for jekyll/jekyll:3.8 
```

Enter fullscreen mode Exit fullscreen mode

我遇到的一件事就是这个警告

```
Error: could not read file /srv/jekyll/vendor/bundle/gems/jekyll-3.8.5/lib/site_template/_posts/0000-00-00-welcome-to-jekyll.markdown.erb: Invalid date '<%= Time.now.strftime('%Y-%m-%d %H:%M:%S %z') %>': Document 'vendor/bundle/gems/jekyll-3.8.5/lib/site_template/_posts/0000-00-00-welcome-to-jekyll.markdown.erb' does not have a valid date in the YAML front matter.
ERROR: YOUR SITE COULD NOT BE BUILT:
       ------------------------------------
       Invalid date '<%= Time.now.strftime('%Y-%m-%d %H:%M:%S %z') %>': Document 'vendor/bundle/gems/jekyll-3.8.5/lib/site_template/_posts/0000-00-00-welcome-to-jekyll.markdown.erb' does not have a valid date in the YAML front matter. 
```

Enter fullscreen mode Exit fullscreen mode

为了解决这个问题，我必须修改我项目的`_config.yml`文件。在你的项目中，如果你已经定义了一个`excluded`部分，添加`vendor`到其中。

```
exclude:
  - "package.json"
  - "README.md"
  - "publish.sh"
  - "vendor" 
```

Enter fullscreen mode Exit fullscreen mode

现在，从上面运行`docker run`命令应该可以工作了，您应该看到您的站点构建在项目的`_site`文件夹中。如果你已经用`git`对你的项目进行了版本控制，现在将`vendor`文件夹添加到你的`.gitignore`文件中可能也是一个好主意。

## 用 Docker 服务你的 Jekyll 项目

如果您想为您的 Jekyll 站点提供一些本地开发服务，而不是编译，您也可以通过运行以下命令使用 Docker 来完成这些工作，

```
docker run --rm --volume="$PWD:/srv/jekyll" --volume="$PWD/vendor/bundle:/usr/local/bundle" --env JEKYLL_ENV=development -p 4000:4000 jekyll/jekyll:3.8 jekyll serve 
```

Enter fullscreen mode Exit fullscreen mode

上面的`serve`命令和`build`命令的唯一区别是我给它传递了一个环境变量`JEKYLL_ENV=development`，正如我提到的，这是因为我的站点的某些部分被设置为只在生产环境中构建。

在运行上面的命令时，您将看到相同的输出，就好像您在计算机上本地安装了 Jekyll 一样，

```
ruby 2.6.3p62 (2019-04-16 revision 67580) [x86_64-linux-musl]
Configuration file: /srv/jekyll/_config.yml
            Source: /srv/jekyll
       Destination: /srv/jekyll/_site
 Incremental build: disabled. Enable with --incremental
      Generating... 
                    done in 19.698 seconds.
 Auto-regeneration: enabled for '/srv/jekyll'
    Server address: http://0.0.0.0:4000/
  Server running... press ctrl-c to stop. 
```

Enter fullscreen mode Exit fullscreen mode

唯一不同的是，现在你的 Jekyll 项目是从 Docker 容器中生成和提供的，你可以将浏览器指向`localhost:4000`并查看 Docker 提供的站点。

———-

感谢您花时间阅读这篇文章！我很乐意通过我的时事通讯给您发送更多的编程技巧。[点击这里报名](http://eepurl.com/bGXerj)。