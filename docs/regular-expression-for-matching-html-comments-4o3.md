# 📦用于匹配 HTML 注释的正则表达式

> 原文：<https://dev.to/samandar/regular-expression-for-matching-html-comments-4o3>

```
from functorflow import f

#Regular expression for matching HTML comments 
with_comment = '<!DOCTYPE html><!--[if lt IE 7]>      <html class="no-js lt-ie9 lt-ie8 lt-ie7"> <![endif]--><html lang="en"><head><meta charset="UTF-8">Document</head><body></body></html>'

no_comment = '<!DOCTYPE html><html lang="en"><head><meta charset="UTF-8">Document</head><body></body></html>'

print(f('html-comment', with_comment)) # True 
print(f('html-comment', no_comment)) # False 
```

Enter fullscreen mode Exit fullscreen mode[https://repl.it/@functorflow/html-comment?lite=true](https://repl.it/@functorflow/html-comment?lite=true)