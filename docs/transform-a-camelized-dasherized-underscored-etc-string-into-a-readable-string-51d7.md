# 📦将骆驼化/锐化/下划线/etc 字符串转换为可读字符串

> 原文：<https://dev.to/samandar/transform-a-camelized-dasherized-underscored-etc-string-into-a-readable-string-51d7>

```
from functorflow import f

print(f('clean-string', 'fooBar'))
# 'Foo bar' 
print(f('clean-string', 'lorem-ipsum'))
# 'Lorem ipsum' 

print(f('clean-string', 'BigBang_theory'))
# 'Big bang theory' 
```

Enter fullscreen mode Exit fullscreen mode[https://repl.it/@functorflow/clean-string?lite=true](https://repl.it/@functorflow/clean-string?lite=true)