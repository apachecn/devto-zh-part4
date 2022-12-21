# 未来 MacOS 不会附带脚本语言。(Python/Ruby/Perl/etc)

> 原文：<https://dev.to/cookrdan/macos-won-t-be-shipped-with-scripting-languages-in-the-future-python-ruby-perl-etc-596o>

刚刚看到[这篇文章](https://www.macobserver.com/analysis/macos-catalina-deprecates-unix-scripting-languages/)。我只是把这个传给你，以防你不像我一样知道。**我推荐阅读那篇文章**，但简短而甜蜜的版本是，MacOS 10.15 (Catalina)有贬值，10.16 将不附带脚本语言。

正如他们在文章中引用的，我将分享来自 [MacOS 10.15 beta 发行说明](https://developer.apple.com/documentation/macos_release_notes/macos_10_15_beta_release_notes)的重要部分:

> 为了与传统软件兼容，macOS 中包含了 Python、Ruby 和 Perl 等脚本语言运行时。默认情况下，未来版本的 macOS 将不包含脚本语言运行时，可能需要您安装额外的软件包。如果您的软件依赖于脚本语言，建议您将运行时捆绑在应用程序中。(49764202)
> 
> 不建议使用 Python 2.7，因为该版本包含在 macOS 中，以便与传统软件兼容。未来版本的 macOS 将不包含 Python 2.7。相反，建议您在终端中运行 python3。(51097165)

看到这个我有点震惊，但同时也不震惊。

有什么想法吗？