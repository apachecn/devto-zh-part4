# 清理 Wordpress:网站安全的经验教训

> 原文：<https://dev.to/wesleylhandy/cleaning-up-wordpress-lessons-learned-in-website-security-2l2f>

[![Someone holding a laptop with 'You've been hacked!' displayed upon the screen](img/62ca7ef8ac5960b00f372fceb7170cf9.png "Someone holding a laptop with 'You've been hacked!' displayed upon the screen")](///static/66c5a6070912c2b67175cdce5e6880a3/c35de/hacked.jpg)

你已经在一个项目上工作了几个月，你已经把钥匙交给了客户，然后你收到了那封可怕的电子邮件...

> ...在第[x]页，一些用户遇到了奇怪的广告和弹出窗口。我觉得网站被黑了！我不知道该怎么办。请帮帮忙！...

我最近就面临这种情况。我自愿为一个非营利组织完成一个项目(不是我朝九晚五工作的那个)。当他们最初雇佣的开发人员为他们创建新网站时，他们已经工作了一年。

我是一个 JavaScript 爱好者，所以我对整个`Wordpress`生态系统并不太熟悉，但是我想我知道的足够多了，我一直在为通过一个短代码注入一个`React`应用程序的工作构建自己的插件，心想，“这能有多难？这甚至不是真正的发展……”(不要见怪，只是表达我当时的想法)。

我非常清楚`Node.js`环境中的安全问题，但是 ***我总是依赖其他服务来处理托管安全*** 。我听说过`.htaccess`文件，但我从未将自己的 LAMP stack 应用程序从头开始投入生产。自从我越来越关注科技，比如`React`、`Gatsby`、`React Native`，甚至还有一点点`Angular`，我甚至没有想过这个问题。

尽管处理所有的前端请求很容易(就难度而言),让网站按照客户的期望完成， ***我理所当然地认为自己对剩下的内容一无所知。*** 我想分享我在过去两周学到的东西，这样(1)我下次就不会忘记，(2)你就可以避免我犯的很多错误。

我将尝试提供在我最近的旅程中帮助过我的所有资料的链接，并简要地谈论一些更重要的部分。让我们称之为:

## 安全 101

**1。永远雇佣一个你认识或者可以信任的开发人员**

雇佣你能找到的最便宜的开发人员是非常有诱惑力的。但有时你会得到你所付出的。

我喜欢有可以给开发者评分的网站，或者留下公众评论的网站，比如 Upwork 或者 LinkedIn(想推荐别人？).这些有助于为开发人员提供责任感，并为客户提供一些安全感。在这种特殊的情况下，最初的开发者对一个相当复杂的网站收取了很低的费用，并且偷工减料。
链接:

*   [https://www.upwork.com/](https://www.upwork.com/)

**2。永远不要下载和使用未锁定的或`nulled`的主题——从一个声誉好的主题市场支付常规费用。否则，您可能会安装恶意软件！！！**

我甚至没有质疑客户网站上主题的使用。我了解客户，我以为他们会自己购买主题，但结果是他们把它留给了开发者，他们可能出于无知或愿意偷工减料，在网上下载了一个被`wp_vcd`恶意软件破坏的主题。

我只是通过将当前网站与网站离线或处于维护模式时存在的代码库版本进行比较，才发现恶意软件存在了多长时间。

这个特定的恶意软件是在`functions.php`内发现的，它在`wp-includes`内创建了三个文件:`wp-feed.php`、`wp-vcd.php`、`wp-tmp.php`。它用恶意脚本包装页面，还添加了一个脚本，记录有权编辑网站的用户的 IP 地址，并系统地忽略这些用户，因此他们永远不会在自己浏览网站时检测到恶意行为，无论是否登录，无论是否私下浏览。

作为管理员级别的用户登录到远程服务器后，我首先在`wp-includes`文件夹中发现了看起来很奇怪的文件。我拿掉了它们，以为我已经解决了问题。该恶意软件正在创建重定向到域名为`oclaserver`的网站，并将 IP 地址记录到`wp-feed.php`。我使用下面的命令行脚本来查找这些文件。

```
grep -Ril "oclaserver" 
```

*   `grep`在文件中搜索匹配给定模式的行。
*   `R`进行递归搜索，浏览当前目录中的所有文件和目录
*   `i`忽略大小写
*   `l`取消输出，只列出文件名

起初，我认为这解决了问题，但下一次我检查，文件在哪里回来。我删除了文件，然后再次检查，很快，文件又出现了，充满了相同的内容。这意味着要么我的`wpdb`被破坏了，要么一些被 Wordpress 反复调用的文件被破坏了，或者两者都有可能。

***接下来我做了什么？*T3】**

在`phpMyAdmin`中搜索我的数据库之前，我首先识别并删除了所有未使用的主题和插件。文件不断出现。所以我开始在文件中搜索术语。

我发现罪魁祸首是在`wp-temp`中代码设置了一个密码变量。我唯一的想法是，这是一个管理员密码的散列(所以我做了一个记录，以便在清理后更改管理员密码)。我运行了相同的`grep`命令，但是搜索的是那个密码值。

瞧，父主题的`functions.php`亮了。在那里，我发现了一堆黑客代码，它们用恶意脚本包装所有页面，并系统地忽略管理员用户登录的 IP 地址。

删除代码后，我从`wp-includes`内部删除了恶意文件，文件没有返回。我还不确定恶意软件是否已经完全消失，因为我注意到`wp-temp`中的代码正在客户机上创建一个 cookie。

删除代码后，我从`wp-includes`内部删除了恶意文件，文件没有返回。我还不确定恶意软件是否已经完全消失，因为我注意到`wp-temp`中的代码正在客户机上创建一个 cookie。

在更改管理员密码之前，我使用 https://api.wordpress.org/secret-key/1.1/salt/的[重置了`wp-config.php`文件中的`SALT Keys`。](https://api.wordpress.org/secret-key/1.1/salt/)

然后，我清空了浏览器的缓存和 cookies，从一个匿名窗口登录到网站的`wp-admin`。**直到那时，我才重新设置了管理员密码。**

最后，我为自己创建了一个新的管理员用户，并鼓励客户通过 [Google Authenticator 插件](https://wordpress.org/plugins/miniorange-2-factor-authentication/)为他们的登录添加 2FA。

链接:

*   [https://www . getastra . com/blog/911/how-to-fix-WP-vcd-back door-hack-in-WordPress-functions-PHP/](https://www.getastra.com/blog/911/how-to-fix-wp-vcd-backdoor-hack-in-wordpress-functions-php/)
*   [https://stack overflow . com/questions/46219263/PHP-code-in-functions-PHP-of-all-WordPress-websites-on-my-shared-hosting](https://stackoverflow.com/questions/46219263/php-code-in-functions-php-of-all-wordpress-websites-on-my-shared-hosting)
*   [https://labs . su curi . net/WP-vcd-malware-comes-with-null-themes/](https://labs.sucuri.net/wp-vcd-malware-comes-with-nulled-themes/)
*   [https://blog . su curi . net/2016/05/nulled-WordPress-themes-malvertising-black-hat-SEO . html](https://blog.sucuri.net/2016/05/nulled-wordpress-themes-malvertising-black-hat-seo.html)

**3。在您的服务器上，添加一个新用户并删除 root 登录，以使他人更难侵入您的服务器。**

由于客户已经在 Ubuntu 18.04 a 数字海洋 droplet 上安装并运行了他们的网站，以前的开发者可以访问该 droplet。我真的不希望他们能够重新登录，无论他们是否有邪恶的意图。我注意到系统上只有一个根用户，所以我为自己创建了一个新用户:

```
sudo adduser myuniqueusername 
```

然后，我将我的新用户添加到了`sudo`用户组:

```
sudo usermod -aG sudo myuniqueusername 
```

然后我给`myuniqueusername` :
添加了管理权限

```
sudo visudo 
```

在`/etc/sudoers`内:

```
myuniqueusername ALL=(ALL:ALL) ALL 
```

最后，在`/etc/ssh/sshd_config`内:

```
PermitRootLogin no 
```

然后从命令行重启`ssh` :

```
sudo service ssh restart 
```

在那里，做任何必要的事情[为你的新账户](https://www.digitalocean.com/docs/droplets/how-to/add-ssh-keys/create-with-openssh/)创建`ssh`密钥，设置它们，注销，然后`ssh`以你的身份重新登录。

**4。强化服务器上的文件权限，以防止未经授权的更改。在你的 Wordpress 安装的根目录下(可能是`/var/www/html/`)做以下事情:**

找出你的用户和你的服务器属于哪个组，确保你的用户拥有你所有 Wordpress 文件的所有权。

```
sudo groups
sudo usermod -aG groupname myuniqueusername
sudo find . -exec chown myuniqueusername:groupname {} + 
```

将文件更改为只能由所有者(您)写入，只能由其他人读取，将目录更改为只能由您或 wordpress 创建、修改或删除，并防止除您或 Wordpress 之外的任何人读取或写入`wp-config`。

```
sudo find . -type f -exec chmod 664 {} +
sudo find . -type d -exec chmod 775 {} +
sudo chmod 660 wp-config.php 
```

链接:

*   [https://www . smashingmagazine . com/2014/05/proper-WordPress-file system-permissions-ownerships/](https://www.smashingmagazine.com/2014/05/proper-wordpress-filesystem-permissions-ownerships/)
*   [https://WordPress . org/support/article/hardening-WordPress/# changing-file-permissions](https://wordpress.org/support/article/hardening-wordpress/#changing-file-permissions)

**5。通过禁用服务器横幅、强化您的`.htaccess`文件、禁用目录列表、禁用 HTTP 跟踪、防止 MIME 嗅探、防止点击劫持、防止某些形式的跨站脚本以及保护您在`wp-config.php`中的 cookies，为您的`httpd.conf` Apache 配置添加强化功能。**

在`/etc/apache2/httpd.conf`或`/etc/apache2/apache2.conf`内:

```
ServerSignature Off
ServerTokens Prod 
```

然后从命令行:

```
sudo service apache2 restart 
```

在`.htaccess`内，在`# BEGIN Wordpress`和`# END Wordpress`之间插入:

```
Options -Indexes
RewriteEngine On 
RewriteCond %{REQUEST_METHOD} ^TRACE 
RewriteRule .* - [F]
Header set X-Content-Type-Options nosniff
Header always append X-Frame-Options SAMEORIGIN
Header set X-XSS-Protection "1; mode=block" 
```

在`wp-config.php`内，添加到末尾:

```
@ini_set('session.cookie_httponly', true);
@ini_set('session.cookie_secure', true);
@ini_set('session.use_only_cookies', true); 
```

关于上述方法是否是最好的，还有一些讨论，您可以并且也许应该在您的 apache 服务器配置中添加 cookie 硬化，请参见下面的链接。

链接:

*   [https://docs.sucuri.net/warnings/hardening/](https://docs.sucuri.net/warnings/hardening/)
*   [https://geek flare . com/WordPress-x-frame-options-http only-cookie/](https://geekflare.com/wordpress-x-frame-options-httponly-cookie/)
*   [https://WordPress . stack exchange . com/questions/175436/cookie-set-without-httponly-flag](https://wordpress.stackexchange.com/questions/175436/cookie-set-without-httponly-flag)
*   [https://geekflare.com/http-header-implementation/](https://geekflare.com/http-header-implementation/)

**6。添加类似`Sucuri Sanner`、`Limit Login Attempts`的安全插件。**

链接:

*   [https://wordpress.org/plugins/limit-login-attempts/](https://wordpress.org/plugins/limit-login-attempts/)
*   [https://wordpress.org/plugins/sucuri-scanner/](https://wordpress.org/plugins/sucuri-scanner/)

## 最后的想法

上面的列表绝不是详尽的，它只是基于我在过去几周学到的东西。我很乐意接受任何有更多经验的人对以上内容的修正、添加或删减。虽然我不打算主要关注 Wordpress，但通过这次经历，我正在学习越来越多的`LAMP`栈的`LA`，最终，随着我对`Gatsby`的兴趣，我可能会越来越多地参与到生态系统中。

话虽如此， ***多了解网站安全并没有错。*T3】**

即使静态网站是未来的趋势，保持我们的 CMS 和网络服务器的安全也不应该丢失或停留在假设的知识上。