# 📦将相对文件系统路径转换为安全的 URI 路径

> 原文：<https://dev.to/samandar/convert-relative-file-system-paths-into-safe-uri-paths-4ad1>

```
from functorflow import f

# Properly encode URI path segments r = f('uri-path', '../abc/@#$%¨&()[]{}-_=+ß/môòñ 月 قمر')
print(r)
# -> '../abc/%40%23%24%25%C2%A8%26()%5B%5D%7B%7D-_%3D%2B%C3%9F/m%C3%B4%C3%B2%C3%B1%20%E6%9C%88%20%D9%82%D9%85%D8%B1' 

# Also supports paths with Windows directory separators print(f('uri-path', 'a\\b\\c'))
# -> 'a/b/c' 
```

Enter fullscreen mode Exit fullscreen mode[https://repl.it/@functorflow/uri-path?lite=true](https://repl.it/@functorflow/uri-path?lite=true)