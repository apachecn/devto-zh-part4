# 开发人员使用外壳别名的方式

> 原文：<https://dev.to/ama/a-developer-s-way-of-using-shell-aliases-2ba6>

不久前我重新发现了一个老朋友——Bash<sup id="fnref1">[1](#fn1)</sup>别名 <sup id="fnref2">[2](#fn2)</sup> 。我们从来不是最好的朋友，直到现在。我们是在我开始学习计算机科学的时候认识的。我当时参观了思科举办的一个课程，“Linux 基础”或类似的东西。培训师曾提到什么是化名，以及化名有多方便。12 年后，我仍然没有明白，直到最近，这个化名以一种新的方式出现在我面前。从那以后，我们成了最好的朋友。请继续阅读，了解原因。

## 别名有哪些？

那么什么是别名呢？根据 Linux 文档项目<sup id="fnref2">[2](#fn2)</sup>——“Bash 别名本质上只不过是一个键盘快捷键，一个缩写，一种避免键入长命令序列的手段。如果比如我们在 **~/中包含 **`alias lm="ls -l | more"`** 。bashrc** 文件，那么在命令行输入的每个**lm<sup id="fnref3">[3](#fn3)</sup>**将自动替换为 **`ls -l | more`** 。这可以节省大量的命令行输入，避免记住复杂的命令和选项组合。设置别名 **rm="rm -i"** (交互模式删除)可以避免大量的悲伤，因为它可以防止无意中删除重要文件。”换句话说，这是一个真正的时间节省，你知道时间是最宝贵的资产，对不对？

alias 命令内置在许多 shell 中，包括 **ash** 、 **bash** (大多数 Linux 系统上的默认 shell)、 **csh** 和 **ksh** 。别名只能由创建它们的 shell 来识别，并且它们只适用于创建它们的用户，除非该用户是可以为任何用户创建别名的 **root** (即管理)用户。 <sup id="fnref4">[4](#fn4)</sup>

### 列出并创建别名

在 bash shell 中，命令是

```
alias [-p] [name="value"] 
```

Enter fullscreen mode Exit fullscreen mode

例如

```
alias ls-dir="ls -al | grep ^d" 
```

Enter fullscreen mode Exit fullscreen mode

它以**长格式**列出了当前目录列表中的所有目录，包括以(.)
并过滤那些以 **d** 开头的(grep)

`Alias`不带参数或带`-p`选项在标准输出中以别名`name=value`的形式打印别名列表。

### 使别名永久化

如果您使用如上所示的 alias 命令，它将只对当前的终端会话有效，这就是为什么我很少这样使用它，而是将它们永久保存在一个适当的配置文件中，该文件由每个登录会话提供 <sup id="fnref5">[5](#fn5)</sup> ，其名称或位置可能因系统而异。
这些文件通常是**。巴沙尔**，**。bash_profile** 或者**。用户主目录中的配置文件**。
我更喜欢把它们放在单独的**里。bash_aliases** 文件，并将其插入前面提到的文件之一。下面是来自**的一个片段。来自我的 MAC OS X 机器的 bash_profile** 文件:

```
less ~/.bash_profile

....
if [ -f ~/.bash_aliases ]; then
    . ~/.bash_aliases
fi
...... 
```

Enter fullscreen mode Exit fullscreen mode

很好的介绍了。如果您访问[使用点文件配置您的登录会话](http://mywiki.wooledge.org/DotFiles)，可以找到点文件

### 删除别名

要删除别名，我们使用`unalias`命令:

```
unalias [-a] name(s) 
```

Enter fullscreen mode Exit fullscreen mode

从定义的别名列表中删除每个名称。如果提供了 **-a** ，则所有别名定义都将被删除(感谢上帝，这仅适用于当前的终端会话)

## 使用别名的方法

好了，现在你对什么是别名以及如何定义它们有了很好的理解，让我告诉你一些我使用它们的方法。

### 别名为别名命令

首先，如前所述，我将它们永久保存在 **~/中。bash_aliases** 文件

其次，我为`alias`命令想出了一些别名:

```
alias alias-vim='vim ~/.bash_aliases'
alias alias-source='source ~/.bash_profile'
alias alias-grep='alias | grep' 
```

Enter fullscreen mode Exit fullscreen mode

这背后的原因是，当我想添加一个新的别名时，我用`alias-vim`命令编辑别名文件，然后我对它进行源文件化。

第三个`alias-grep`命令我发现它很有用，当我需要一些信息时，我知道我可以在一个别名中找到——例如，如果我需要找到我的 GoDaddy 服务器的 IP 地址，我会使用

```
alias-grep ssh-g 
```

Enter fullscreen mode Exit fullscreen mode

> 我知道我可能已经定义了一个别名来通过 ssh 连接到 GoDaddy 主机，并且我得到了预期的结果:

```
alias ssh-godaddy='ssh username@100.121.50.241' 
```

Enter fullscreen mode Exit fullscreen mode

### 别名格式

接下来的几个要点将强调我为别名使用的一些格式。

> 对于包含更多单词的别名，我用破折号(-)分隔单词

#### 快捷键

我使用容易记忆的快捷方式，它们本身没有意义。例如，下面是 ls <sup id="fnref6">[6](#fn6)</sup> 和 maven <sup id="fnref7">[7](#fn7)</sup> 命令的一些片段:

```
#ls
alias ll="ls -lha"; # list files and directories in long format (-l), also hidden ones (-a) with human-readable sizes (-h)

#maven
alias mci="mvn clean install"
alias mcp="mvn clean package"
alias mcis="mvn clean install -DskipTests" 
```

Enter fullscreen mode Exit fullscreen mode

#### 命令加上有意义的文本- `{command}-{relevant}-{text}`

> **非常非常重要** -在这里充分利用自动完成功能，当你使用**标签**按钮时，你可以在大多数终端中使用

我使用的第二种格式是命令本身加上一些对我有意义的内容。
例如:

```
alias ssh-godaddy='ssh username@100.121.50.241' 
```

Enter fullscreen mode Exit fullscreen mode

我通常需要输入`"ssh-g" + tab`，别名就会自动完成。

或者

```
alias ps-grep="ps aux | grep" # e.g."ps-grep java" will list processes that have java in description 
```

Enter fullscreen mode Exit fullscreen mode

#### 上下文加命令加有意义的文本- `{context}-{command}||{context-options}-{relevant}-{text}`

在这种情况下，我使用适用于工具命令的别名(如 tomcat、mysql、nginx 等)。)，后跟在该上下文中有效的 shell 命令或选项。例如:

```
alias tomcat-start="sudo initctl start tomcat" # start tomcat via setup service on Ubuntu System command
alias tomcat-tail-log="tail -f /opt/tomcat/logs/catalina.out" # tail tomcat log command
alias mysql-vim-my.conf="sudo vim /etc/mysql/my.cnf" # edit mysql configuration command
alias nginx-config-verify="sudo nginx -t -c /etc/nginx/nginx.conf" # verify validity of nginx configuration file command 
```

Enter fullscreen mode Exit fullscreen mode

### 高级别名

Bash aliases 不直接支持参数，但是如果需要一些带参数的你可以创建一个函数并给这个函数起别名。例如，如果我想用当前时间戳和一些以破折号分隔的文本来备份文件:

```
alias nginx-config-backup=nginxconfigbackup

nginxconfigbackup(){
  sudo cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.$(date "+%Y-%m-%d_%H:%M")-$1 #the parameter ending is the comment use dashes "-" between words
} 
```

Enter fullscreen mode Exit fullscreen mode

例如，当我执行`nginx-config-backup add-proxy-support`时，会在`/etc/nginx/`目录中产生一个名为`nginx.conf.2016-08-04_17:18-add-proxy-support`的文件。

或者，如果我想从当前目录开始对一个`text`递归调用`grep`，我会写这样的代码:

```
grepRecursivelyForText(){
  grep -ri $1 .
}

alias grep-recursively=grepRecursivleyForText 
```

Enter fullscreen mode Exit fullscreen mode

并且像`grep-recursively some_text`一样使用。

> 我不得不承认，在第二个例子中，别名比命令本身要长，但是对我来说更容易记住，使用自动补全功能也没有问题...

## 个人别名列表

我现在将按字母顺序列出我使用的一些别名，这样你可能会从中找到一些灵感:

### 别名

```
#alias
alias alias-source="source ~/.bash_profile"
alias alias-vim="vim ~/.bash_aliases"
alias alias-grep="alias | grep" 
```

Enter fullscreen mode Exit fullscreen mode

### Ls

```
#ls -- list directory contents
alias ls="ls -CF" # make ls display in columns and with a file type indicator by default("/")
alias ll="ls -l" # -l list in long format
alias llrt="ls -lrt" # list in long format, in reverse order of modification time (I find it easier to see last modified items at the end)
alias ls-dir="ls -al | grep ^d" #  listing of directories in the current directory 
```

Enter fullscreen mode Exit fullscreen mode

### Cd

```
#cd -- change directory
alias ..="cd .."
alias cd..="cd .."
alias cd-projects="cd /Users/ama/projects/"
alias cd-codepediaorg.github.io='cd ~/projects/blogs/codepediaorg.github.io'
alias cd-jboss-eap-6.1-logs="cd ~/dev/as/jboss-eap-6.1/standalone/logs 
```

Enter fullscreen mode Exit fullscreen mode

> `cd`单独将改变到用户的主目录

### Df

```
#df -- display free disk space
alias df="df -h" # "human-readable" output 
```

Enter fullscreen mode Exit fullscreen mode

### [胃](#maven)

```
#maven
alias mci="mvn clean install"
alias mcp="mvn clean package"
alias mcis="mvn clean install -DskipTests" 
```

Enter fullscreen mode Exit fullscreen mode

### 哲基尔

```
#jekyll
alias jekyll-serve-dev="bundle exec jekyll serve --config _config.yml,_config.dev.yml" 
```

Enter fullscreen mode Exit fullscreen mode

### 键盘锁 2.0

```
#keycloak 2.0
alias kc2-cd-home="cd ~/dev/as/jboss-eap-7.0-keycloak-server/"
alias kc2-export-JBOSS_HOME="export JBOSS_HOME=~/dev/as/jboss-eap-7.0-keycloak-server/"
alias kc2-vim-standalone.xml="vim ~/dev/as/jboss-eap-7.0-keycloak-server/standalone/configuration/standalone.xml"
alias kc2-start="~/dev/as/jboss-eap-7.0-keycloak-server/bin/standalone.sh -Djboss.socket.binding.port-offset=200 -Djdk.tls.client.protocols=TLSv1 -b 0.0.0.0 --server-config=standalone.xml" 
```

Enter fullscreen mode Exit fullscreen mode

### Mkdir

```
#mkdir -- make directory
alias mkdir="mkdir -pv" # make necessary parent directory, and make it verbose to help avoid typos 
```

Enter fullscreen mode Exit fullscreen mode

### [MySQL](#mysql)

```
# MySql
alias mysql-start="sudo service mysql start"
alias mysql-stop="sudo service mysql stop"
alias mysql-restart="sudo service mysql restart"
alias mysql-status="sudo service mysql status"
alias mysql-connect-root="mysql -uroot -p"
alias mysql-vim-my.conf="sudo vim /etc/mysql/my.cnf" 
```

Enter fullscreen mode Exit fullscreen mode

### Nginx

```
# Nginx
alias nginx-start="sudo nginx"
alias nginx-stop="sudo nginx -s stop"
alias nginx-restart="sudo service nginx restart"
alias nginx-status="sudo ps aux | grep nginx"
alias nginx-reload="sudo kill -HUP `cat /usr/local/var/run/nginx.pid`"
alias nginx-vim-nginx.conf="sudo vim /usr/local/etc/nginx/nginx.conf"
alias nginx-config-verify="sudo nginx -t -c /usr/local/etc/nginx/nginx.conf"
alias nginx-config-backup=nginxconfigbackup

nginxconfigbackup(){
  sudo cp /usr/local/etc/nginx/nginx.conf /usr/local/etc/nginx/nginx.conf.$(date "+%Y-%m-%d_%H:%M")-$1 #the parameter ending is the comment use dashes "-" between words
} 
```

Enter fullscreen mode Exit fullscreen mode

### 【NPM】[【https://www . npmj . com/](https://www.npmjs.com/)

```
#npm
alias npmi="npm install"
alias npmig="npm install -g" # -g install globally 
```

Enter fullscreen mode Exit fullscreen mode

### Ps

```
#ps -- process status
alias ps-grep="ps aux | grep" # e.g."ps-grep java" will list processes that have java in description 
```

Enter fullscreen mode Exit fullscreen mode

### Rhc

```
#rhc -- OpenShift client tools
alias rhc-tail-jbosseap.log="rhc tail -o "-n100" -f app-root/logs/jbosseap.log -a heappbackendlarge -n bkwtest"
alias rhc-tail-homeenergy.log="rhc tail -o "-n100" -f app-root/logs/homeenergy.log -a heappbackendlarge -n bkwtest"
alias rhc-ssh-heappbackendlarge="rhc ssh -a heappbackendlarge -n bkwtest"
alias rhc-port-forward-heappbackendlarge="rhc port-forward -a heappbackendlarge -n bkwtest" 
```

Enter fullscreen mode Exit fullscreen mode

> 在这里，您可以真正看到别名是多么强大和省时...

### 雄猫

```
# tomcat
alias tomcat-start="/opt/tomcat/bin/startup.sh"
alias tomcat-stop="/opt/tomcat/bin/shutdown.sh"
alias tomcat-status="sudo ps aux | grep tomcat"
alias tomcat-cd-webapps="cd /opt/tomcat/webapps"
alias tomcat-copy-root-to-apps="sudo cp $HOME/projects/podcastpedia/web-ui/target/ROOT.war /opt/tomcat/webapps"
alias tomcat-show-log="tail -f /opt/tomcat/logs/catalina.out"
alias tomcat-backup="cp /opt/tomcat/webapps/ROOT.war \"$HOME/podcastpedia/ROOT.war.backup.$(date +%F_%R)\""
alias tomcat-remove-root="rm -rf /opt/tomcat/webapps/ROOT*" 
```

Enter fullscreen mode Exit fullscreen mode

## 视频概念介绍

[https://www.youtube.com/embed/Emlc7mkZDQ4](https://www.youtube.com/embed/Emlc7mkZDQ4)

## 结论

只要能让你的终端生活更轻松，所有别名都是好别名:)。我希望你喜欢这个，如果你有任何建议，请在下面留下评论。

> 这篇文章最初发表在[开发人员使用别名指南](https://www.codepedia.org/ama/a-developers-guide-to-using-aliases/)

* * *

1.  [https://www . GNU . org/software/bash/manual/html _ node/what-is-bash _ 003 f . html](https://www.gnu.org/software/bash/manual/html_node/What-is-Bash_003f.html)↩

2.  [http://tldp.org/LDP/abs/html/aliases.html](http://tldp.org/LDP/abs/html/aliases.html)↩

3.  ...作为命令字符串的第一个单词。显然，别名只在命令的**开始**处有意义。 [↩](#fnref3)

4.  [http://www.linfo.org/alias.html](http://www.linfo.org/alias.html)↩

5.  [http://ss64.com/bash/source.html](http://ss64.com/bash/source.html)↩

6.  http://linuxcommand . org/man _ pages/ls 1 . html

7.  [https://maven.apache.org/](https://maven.apache.org/)↩