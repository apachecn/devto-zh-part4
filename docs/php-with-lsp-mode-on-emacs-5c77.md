# Emacs 上带 lsp 模式的 PHP

> 原文：<https://dev.to/gayanhewa/php-with-lsp-mode-on-emacs-5c77>

[![](img/a34d806167a260cc07f52eca52d6437a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kE29D7Zm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y9agkdgmf6uikx9oywgq.png)

Emacs 是一个相当不错的编辑器。当我开始使用 Emacs 时，它最初只用于 org-mode。因为没有像 org-mode 这样的东西。纯粹的天才。我不想一开始就试图转而使用 emacs 来编码，这让我不知所措。我主要使用 Go、PHP 和 Node。所以我最初的设置是在我通常的 vscode 设置中只使用 emacs 作为组织模式和代码。逐渐地，将每种语言设置转移到 emacs。到目前为止，我已经把 Go 和 PHP 转移到 emacs。节点，暂时还在 vscode。

在 Emacs 中设置 Go 轻而易举。如此美丽的语言和整洁的生态系统很容易在任何地方设置。另一方面，PHP 本身就是一头野兽。我对 PHP 的初始设置是让适当的语法高亮、林挺和语言服务器协议按预期工作。

对于基本的 php 模式设置:

```
;;; -\*- lexical-binding: t -\*-

(use-package php-mode
 :ensure t
 :mode
 (“\\.php\\’” . php-mode))

(add-to-list ‘auto-mode-alist ‘(“\\.php$” . php-mode))

(use-package phpunit
 :ensure t)

(provide ‘lang-php) 
```

并启用 lsp 模式，

```
(use-package company
 :ensure t
 :config
 (setq company-idle-delay 0.3)
 (global-company-mode 1)
 (global-set-key (kbd “C-\<tab\>”) ‘company-complete))

(use-package flycheck)

(use-package lsp-mode
 :config
 (setq lsp-prefer-flymake nil)
 :hook (php-mode . lsp)
 :commands lsp)

(use-package lsp-ui
 :requires lsp-mode flycheck
 :config
 (setq lsp-ui-doc-enable t
 lsp-ui-doc-use-childframe t
 lsp-ui-doc-position ‘top
 lsp-ui-doc-include-signature t
 lsp-ui-sideline-enable nil
 lsp-ui-flycheck-enable t
 lsp-ui-flycheck-list-position ‘right
 lsp-ui-flycheck-live-reporting t
 lsp-ui-peek-enable t
 lsp-ui-peek-list-width 60
 lsp-ui-peek-peek-height 25
 lsp-ui-sideline-enable nil)

(add-hook ‘lsp-mode-hook ‘lsp-ui-mode))

(use-package company-lsp
 :commands company-lsp) 
```

lsp-mode 包中设置的:hooks 是我们为每种语言模式启用 lsp-server 的地方。

现在，有了所有这些配置，我们需要决定我们的 PHP 将使用哪种语言服务器

1.  [https://github.com/tsufeki/tenkawa-php-language-server](https://github.com/tsufeki/tenkawa-php-language-server)
2.  [https://github.com/felixfbecker/php-language-server](https://github.com/felixfbecker/php-language-server)
3.  [http://intelephense.net/](http://intelephense.net/)

多年来，我个人一直使用 Felixbeckers 的 LSP 实现和 vscode。除了一些小问题之外，它总体上很好，并能像预期的那样工作。由于 emacs lsp 模式插件内置于 intelephense 中，我们只需运行

```
npm i -g intelephense 
```

每次我们在 emacs 上开发 PHP 项目时，它都会被启动。一旦疗程结束就被杀死。

lsp-ui 只是让它在编辑器上看起来漂亮的糖，但是显然没有它也可以生活，并且可以通过键绑定来触发这些事件，用于搜索、引用、完成和静态分析。

我保留了我的不完整，搞乱了 Github 上的配置。它混合了

1.  ## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png)T4】弗里托尼克 / [卡斯尔麦斯](https://github.com/freetonik/castlemacs)

    ### 现代、简约的苹果电脑⌘版 emacs

    t11t13

    <article class="markdown-body entry-content p-5" itemprop="text">t15】castlemacs:⌘苹果公司的现代极简 emacs

    [![https://img.shields.io/github/tag/freetonik/castlemacs.svg?label=release&style=flat-square](img/b08e1fe799d032b8cfe47e864a032747.png)](https://camo.githubusercontent.com/6ae449165c8b8e6849e7147a9ac29669bc0f83c6/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f7461672f66726565746f6e696b2f636173746c656d6163732e7376673f6c6162656c3d72656c65617365267374796c653d666c61742d737175617265)[![https://img.shields.io/badge/license-MIT-green.svg?style=flat-square](img/b378aae478640172bd104c9d6217df6a.png)](https://camo.githubusercontent.com/95e854794a291423fe200ec681d09ed63f9fadd1/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f6c6963656e73652d4d49542d677265656e2e7376673f7374796c653d666c61742d737175617265)

    [![./screenshots/main2.png](img/96e901e7c2485ae241e56177314b91d2.png)T2](https://raw.githubusercontent.com/freetonik/castlemacs/master/README.org/./screenshots/main2.png)

    * * *

    [安装](https://raw.githubusercontent.com/freetonik/castlemacs/master/README.org/#installation) | [变更日志](https://raw.githubusercontent.com/freetonik/castlemacs/master/README.org/CHANGELOG.org)T36

    * * *

    T38】特征

    *   与普通 macOS 键盘兼容
    *   符合人体工程学的键盘， 明智的规则
    *   轻松的窗口管理和移动
    *   轻松的文件内点间移动和文件间移动
    *   多个光标、项目管理器、Git 前端、文件树、终端
    *   方便的拼写检查器、内置的词库和单词定义查找
    *   一大把微小、 有用的助手功能

    # 目录

    *   [简介](https://raw.githubusercontent.com/freetonik/castlemacs/master/README.org/#intro)
        *   [核心原则](https://raw.githubusercontent.com/freetonik/castlemacs/master/README.org/#core-principles)
    *   [常见问题](https://raw.githubusercontent.com/freetonik/castlemacs/master/README.org/#faq)
    *   [安装](https://raw.githubusercontent.com/freetonik/castlemacs/master/README.org/#installation)
        *   [安装 Emacs](https://raw.githubusercontent.com/freetonik/castlemacs/master/README.org/#install-emacs)
            *   [备选版本(高级)](https://raw.githubusercontent.com/freetonik/castlemacs/master/README.org/#alternative-version-advanced)
        *   [安装依赖项](https://raw.githubusercontent.com/freetonik/castlemacs/master/README.org/#install-dependencies)
        *   [安装 Castlemacs](https://raw.githubusercontent.com/freetonik/castlemacs/master/README.org/#install-castlemacs)
        *   [安装键盘](https://raw.githubusercontent.com/freetonik/castlemacs/master/README.org/#setup-keyboard)
        *   [Mojave 用户注意事项](https://raw.githubusercontent.com/freetonik/castlemacs/master/README.org/#note-to-mojave-users)
    *   [用法](https://raw.githubusercontent.com/freetonik/castlemacs/master/README.org/#usage)
        *   [修改键](https://raw.githubusercontent.com/freetonik/castlemacs/master/README.org/#modifier-keys)

    *   [搜索和替换](https://raw.githubusercontent.com/freetonik/castlemacs/master/README.org/#search-and-replace)*   [编辑](https://raw.githubusercontent.com/freetonik/castlemacs/master/README.org/#editing)
        *   [字和行](https://raw.githubusercontent.com/freetonik/castlemacs/master/README.org/#words-and-lines)
        *   [删除文本](https://raw.githubusercontent.com/freetonik/castlemacs/master/README.org/#deleting-text)
        *   [多个光标](https://raw.githubusercontent.com/freetonik/castlemacs/master/README.org/#multiple-cursors)
        *   [缩进](https://raw.githubusercontent.com/freetonik/castlemacs/master/README.org/#indentation)
        *   [Git](https://raw.githubusercontent.com/freetonik/castlemacs/master/README.org/#git)
            *   [Git gutter](https://raw.githubusercontent.com/freetonik/castlemacs/master/README.org/#git-gutter)
            *   [Magit](https://raw.githubusercontent.com/freetonik/castlemacs/master/README.org/#magit)
        *   [终端(shell)](https://raw.githubusercontent.com/freetonik/castlemacs/master/README.org/#terminal-shell)
        *   [文件树并打开缓冲区](https://raw.githubusercontent.com/freetonik/castlemacs/master/README.org/#file-tree-and-open-buffers)
        *   [编程](https://raw.githubusercontent.com/freetonik/castlemacs/master/README.org/#programming)</article>

2.  [http://emacs-bootstrap.com/](http://emacs-bootstrap.com/)