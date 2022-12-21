# 在组织模式下突出显示

> 原文：<https://dev.to/gonsie/highlighting-in-org-mode-291h>

我使用 org-mode 做很多笔记，并且我经常想要强调一些我正在定义的术语或关键句子。总的来说，我想要两样东西:

1.  我想添加一些标记语法，这将突出一个词或短语，使用“:”来标记短语的开始和结束(类似于粗体或斜体标记)。
2.  当我通过 HTML(或 markdown)导出这个标记时，它应该被转换成 HTML5 `<mark>`标签。

## 扩展组织语法

扩展 org 语法并不是最简单的。起初，我试图修改`org-emphasis-alist`变量，但这似乎不起作用。相反，我可以用一个额外的正则表达式来扩展`org-font-lock`。

```
(defun org-add-my-extra-markup ()
  "Add highlight emphasis."
  (add-to-list 'org-font-lock-extra-keywords
               '("[^\\w]\\(:\\[^\n\r\t]+:\\)[^\\w]"
                 (1 '(face highlight invisible nil)))))

(add-hook 'org-font-lock-set-keywords-hook #'org-add-my-extra-markup) 
```

elisp 正则表达式不是最容易理解的，但它匹配的内容如下:

| 正则表达式 | 说明 |
| --- | --- |
| `[^\\w]` | 不是一个词的东西 |
| `\\(` | 捕获组的开始(不匹配) |
| `:` | 一个冒号 |
| `[^\n\r\t]+` | 由一个或多个字符组成的字符串，不包含换行符或制表符 |
| `:` | 一个冒号 |
| `\\)` | 捕获组的结尾(不匹配) |
| `[^\\w]` | 不是一个词的东西 |

非单词内容的首尾匹配意味着 URL 中间的冒号不匹配，突出显示的文本后面可以跟标点符号。括号创建一个捕获组，稍后用于定义什么具有高亮显示面。以下是尝试解释不同部分的图表:

```
'("[^\\w]\\(:\\[^\n\r\t]+:\\)[^\\w]" (1 '(face highlight invisible nil)))
  |      '--capture group---'      |  | '--face definition-----------'
  '--regular expression to match---'  '--capture group number 
```

## 导出 HTML

下一步是扩展 org-exporter。这很容易做到，甚至在[出口发动机手册](https://orgmode.org/manual/Advanced-Export-Configuration.html#Advanced-Export-Configuration)中提供了一个工作示例。我只是为构建在 html 之上的任何后端创建了一个过滤器(应该包括我的 markdown 和 jekyll-md 导出器)。

```
(defun my-html-mark-tag (text backend info)
  "Transcode :blah: into <mark>blah</mark> in body text."
  (when (org-export-derived-backend-p backend 'html)
    (let ((text (replace-regexp-in-string "[^\\w]\\(:\\)[^\n\t\r]+\\(:\\)[^\\w]" "<mark>" text nil nil 1 nil)))
      (replace-regexp-in-string "[^\\w]\\(<mark>\\)[^\n\t\r]+\\(:\\)[^\\w]" "</mark>" text nil nil 2 nil))))

(add-to-list 'org-export-filter-plain-text-fucntions 'my-html-mark-tag) 
```

我将此添加到我的`org-config.el`文件中，成功了！

## 替代品

当然，org-mode 已经附带了一些内置的高亮功能；即定义描述列表的语法(这是一个本地 HTML 概念)。以下组织格式可用于创建描述列表:

```
- Emacs :: an extensible ,customizable, free/libre text editor
- Org mode :: keeping notes, maintaining TODO lists, planning projects, and authoring documents with a fast and effective plain-text system 
```

它变成了下面的 HTML:

```
<dl>
<dt>Emacs</dt><dd>an extensible ,customizable, free/libre text editor</dd>
<dt>Org mode</dt><dd>keeping notes, maintaining TODO lists, planning projects, and authoring documents with a fast and effective plain-text system</dd>
</dl> 
```

虽然这是高亮显示的一个常见用例，但我并不总是希望我的高亮显示在一个列表中。

## 最后一个例子

这里是一个非常简单的例子，一个正确导出的<mark>高亮短语</mark>。我还可以用这个方法来突出显示一个更长的句子，比如:<mark>我应该实现这个突出显示语法并将功能导出到 Orgmode 本身吗？</mark>