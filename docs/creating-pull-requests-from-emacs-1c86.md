# 从 emacs 创建拉式请求

> 原文：<https://dev.to/prathamesh/creating-pull-requests-from-emacs-1c86>

[![Creating pull requests from emacs](img/a84104ec0446250f057f9cca436a859e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--o4BihTGf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://prathamesh.tech/conteimg/2019/06/callum-shaw-555357-unsplash.jpg)

我使用 [magit](https://magit.vc/) 来处理 git 和 emacs。Magit 可以很容易地创建分支并将其推送到 github。创建一个分支后，自然的事情就是创建一个拉请求。但是你必须访问 github 并点击“新的拉请求”来创建拉请求。

我想从 emacs 和 magit 中自动创建拉取请求。在谷歌搜索了这个问题后，我发现了[这个解决方案](https://endlessparentheses.com/create-github-prs-from-emacs-with-magit.html)。当我添加了这段代码并尝试使用键盘快捷键`v`创建拉请求时，emacs 使用`w3m`浏览器打开了页面。我想用我默认的浏览器 chrome 打开这个网址。我们可以使用下面的代码告诉 emacs 使用默认浏览器打开 URL。

```
;; Set default browser as default OS X browser
(setq browse-url-browser-function 'browse-url-default-macosx-browser) 
```

Enter fullscreen mode Exit fullscreen mode

这可以确保 emacs 试图打开的任何 URL 都是在 Mac OS X 系统的默认浏览器中打开的。对于 windows，也有类似的配置。

```
(setq browse-url-browser-function 'browse-url-default-windows-browser) 
```

Enter fullscreen mode Exit fullscreen mode

现在，我可以轻松地从 emacs 打开拉请求。我只需要在浏览器中登录 github。如果您使用 gitlab 托管代码，emacs 作者的原始解决方案也可以扩展到使用 gitlab。

```
;; original

(defun endless/visit-pull-request-url ()
  "Visit the current branch's PR on Github."
  (interactive)
  (browse-url
   (format "https://github.com/%s/pull/new/%s"
           (replace-regexp-in-string
            "\\`.+github\\.com:\\(.+\\)\\.git\\'" "\\1"
            (magit-get "remote"
                       (magit-get-push-remote)
                       "url"))
           (magit-get-current-branch))))

(eval-after-load 'magit
  '(define-key magit-mode-map "v"
     #'endless/visit-pull-request-url)) 
```

Enter fullscreen mode Exit fullscreen mode

T2】

```
;;; modified for gitlab
(defun endless/visit-pull-request-url-gitlab ()
  "Visit the current branch's PR on Gitlab."
  (interactive)
  (browse-url
   (format "https://gitlab.com/%s/pull/new/%s"
           (replace-regexp-in-string
            "\\`.+gitlab\\.com:\\(.+\\)\\.git\\'" "\\1"
            (magit-get "remote"
                       (magit-get-push-remote)
                       "url"))
           (magit-get-current-branch))))

(eval-after-load 'magit
  '(define-key magit-mode-map "h"
     #'endless/visit-pull-request-url-gitlab)) 
```

Enter fullscreen mode Exit fullscreen mode

这段代码适用于 magit 版本`20190620.2234`以及 Git 2.20.1 和 Emacs 26.1

订阅我的[简讯](https://www.prathamesh.tech/mailing-list)了解更多关于 Emacs 和 magit 的有趣见解。

黑客快乐！