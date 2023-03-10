# PHP 7.4 预加载介绍

> 原文：<https://dev.to/restoreddev/introduction-to-php-7-4-preload-4lg4>

PHP 7.4 是 PHP 编程语言的最新版本，将于今年晚些时候发布。PHP 7.4 最近进入测试版，所以我开始探索新的特性。一个很有前途的特性叫做 [Opcache Preload](https://wiki.php.net/rfc/preload) 。使用 preload，你可以设置 PHP 在启动时将某些 PHP 文件加载到内存中。这样做减少了 PHP 在接收 HTTP 请求时必须编译的文件数量，从而提高了性能。在探索 preload 之前，让我们看看 opcache 在 PHP 运行时是如何工作的。

## Opcache 是你的朋友

在 PHP 中，术语 PHP 用于编程语言。然而，执行 PHP 代码的运行时被称为 Zend 引擎。当 Zend 引擎接收到对 PHP 文件的请求时，它实际上将 PHP 文件编译成操作码列表，因此有了术语`opcode`。在编译完一个 PHP 文件后，Zend 引擎扩展将这些操作码存储在内存中，这样在后续的文件请求中就不必再次编译 PHP 文件。

当 Zend 引擎被激活时,`opcache.preload`指令允许 PHP 程序员指示 opcache 缓存一组 PHP 文件的操作码。预加载选项应该指向一个 PHP 文件，该文件对您想要预加载的文件运行函数 [`opcache_compile_file`](https://www.php.net/manual/en/function.opcache-compile-file.php) 。

## 启用预载

为了测试预加载，我设置了一个使用 [Slim 3](http://www.slimframework.com/docs/v3/) 进行路由的[示例项目](https://github.com/restoreddev/preloading-example)。

要使用预加载，你必须安装 PHP 7.4。我在激活了 [ondrej/php](https://launchpad.net/~ondrej/+archive/ubuntu/php) PPA 的情况下在 Windows 上使用 Ubuntu WSL。因为我使用的是 Ondrej 的 PPA，所以我可以在 bash 中运行`sudo apt install php7.4-fpm`来获得我的系统上最新的 7.4 测试版。我使用 FPM 安装的 Nginx 作为我的 web 服务器，一个名为`preloading.local`的主机条目指向我的项目。安装完成后，我打开了 FPM php.ini 文件(在我的系统上位于`/etc/php/7.4/fpm/php.ini`,并将`opcache.preload`选项改为我的项目中的`preload.php`文件的路径。

根据 Dmitry Stogov 在[预加载 RFC](https://wiki.php.net/rfc/preload) 中的示例，`preload.php`文件使用了修改后的`preload`函数。我使用 Composer 将所有的小依赖项加载到一个`vendor`文件夹中。然后，我在运行 Slim 应用程序所需的所有 Composer 源文件上使用我的`preload.php`中的`preload`函数。重启 PHP FPM ( `sudo service php7.4-fpm restart`)后，我可以在浏览器中打开`preloading.local`，看到我的应用程序成功运行。

## 我对预载的想法

1.  我喜欢我不必为我的框架使用 Composer autoloader。因为所有的框架代码都是预加载的，所以自动加载是不必要的。我真的希望 Composer 会生成一个自动的`preload.php`文件，就像`autoload.php`文件一样，这样就可以很容易地预加载供应商依赖项。
2.  对于像这样的小项目来说，性能的提升微乎其微，但是，我认为预加载是加速大型框架(如 Symfony、Laravel，尤其是 Magento)的好方法。
3.  如果你在同一个服务器上有多个 PHP 项目，那么预加载将很难实现，因为你只能设置预加载一个文件。
4.  我遇到了一个问题，预加载无法加载`nikic/fast-route`模块中的`functions.php`文件，因为它被包装在一个`if`语句中。我必须建立一个补丁文件，在没有`if`语句的情况下手动加载这些函数。可能是因为 7.4 还在测试中，或者我的`preload.php`需要改进。
5.  我不认为很多 PHP 开发人员会在开发环境中使用 preload。我通常不愿意在生产中使用在开发中没有使用的特性，因为在生产服务器上可能会出现看不见的问题。然而，预加载的风险很低，所以启用它还是值得的。

总而言之，我非常期待预加载带来的性能提升。PHP 已经是网络上最快的脚本语言之一，我很高兴核心团队继续将 PHP 推向更快的领域。

接下来，我想试试[箭头函数](https://wiki.php.net/rfc/arrow_functions_v2)、[对外函数接口](https://wiki.php.net/rfc/ffi)和[类型化属性](https://wiki.php.net/rfc/typed_properties_v2)。PHP 很快会增加很多很棒的特性！