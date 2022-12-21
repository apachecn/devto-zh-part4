# Japanese Problem note (主要总结日语乱码对应的笔记)

> 原文：<https://dev.to/n350071/japanese-problem-note-4f9c>

我用日语写这篇笔记，因为这篇笔记将帮助解决日语字符集的问题。

## MySQL 中的日语乱码

登录在 Docker 等上运行的 MySQL，选择一下，我想会不会出现日语变成乱码读不懂的情况。

例如，是这样的。

```
mysql> select post_type, post_title from wp_posts where ID = 21787;
+-----------+--------------+
| post_type | post_title   |
+-----------+--------------+
| blog      | ????????BBQ! |
+-----------+--------------+
1 row in set (0.00 sec) 
```

Enter fullscreen mode Exit fullscreen mode

调查了一下字符编码，发现字符编码上好像不能使用 latin1 这个日语。

```
mysql> SHOW VARIABLES LIKE 'character_set%';
+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | latin1                     |
| character_set_connection | latin1                     |
| character_set_database   | latin1                     |
| character_set_filesystem | binary                     |
| character_set_results    | latin1                     |
| character_set_server     | latin1                     |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
+--------------------------+----------------------------+
8 rows in set (0.00 sec) 
```

Enter fullscreen mode Exit fullscreen mode

将这个会话中使用的字符编码设为 utf8，以便可以显示日语。

```
mysql> CHARSET utf8;
Charset changed 
```

Enter fullscreen mode Exit fullscreen mode

我再取出刚才的唱片看看。

```
mysql> select post_type, post_title from wp_posts where ID = 21787;
+-----------+--------------+
| post_type | post_title   |
+-----------+--------------+
| blog      | たのしいBBQ!  |
+-----------+--------------+
1 row in set (0.00 sec) 
```

Enter fullscreen mode Exit fullscreen mode

补充: (虽然没有尝试)今后会有文字对应等内容，所以`CHARSET utf8mb4`可能更好。

## Docker 环境内的 Rails console 的日语乱码

在`Dockerfile`的环境变量 LANG 中设定 UTF-8。

```
FROM ruby:2.6.3

# railsコンソール中で日本語入力するための設定
ENV LANG C.UTF-8 
```

Enter fullscreen mode Exit fullscreen mode

## binding.pry 上的日语乱码

### status

输入日语后会出现“未定义字符编码转换”错误。

```
Encoding::UndefinedConversionError:
       "\xE3" from ASCII-8BIT to UTF-8 
```

Enter fullscreen mode Exit fullscreen mode

### 基本的应对

不再使用 pry 的历史记录([参考](https://altarf.net/computer/rails/3222))。

```
# ~/.pryrc
Pry.config.history.should_save = false 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果使用的是名为“rb-readline”的 gem，则无法正常工作，请尝试以下方法。

### 重新安装

首先一般卸载

```
rbenv uninstall 2.x.x 
```

Enter fullscreen mode Exit fullscreen mode

然后，使用以下选项在 rbenv 中进行安装。

```
# CONFIGURE_OPTS="--with-readline-dir=`brew --prefix readline`" rbenv install 2.x.x
RUBY_CONFIGURE_OPTS=--with-readline-dir=`brew --prefix readline` rbenv install --verbose 2.x.x 
```

Enter fullscreen mode Exit fullscreen mode

### The last resort

使用 NKF 的

```
[2] pry> find("���������������")  #=> Encoding::UndefinedConversionError: "\xE8" from ASCII-8BIT to UTF-8
[4] pry> Encoding.default_external  #=> #<Encoding:UTF-8>
[5] pry> require 'nkf'              #=> false
[6] pry> Encoding.default_internal  #=> #<Encoding:UTF-8>
[7] pry> 'a'.encoding               #=> #<Encoding:UTF-8>
[8] pry> '���'.encoding             #=> #<Encoding:ASCII-8BIT>

click_link NKF.nkf('-w','リンクをクリックしてください') 
```

Enter fullscreen mode Exit fullscreen mode

* * *

如果再发现问题的话，我想补充一下。