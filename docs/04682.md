# 需要带有 jscodeshift 帮助的 codedom

> 原文：<https://dev.to/email2vimalraj/codemod-with-jscodeshift-help-needed-629>

我正在尝试编写一个小 codemod 来重构一些代码。考虑一下我有这样的东西:

```
import { mod1, mod2, mod3 } from 'package1'
import localMod from 'package2' 
```

Enter fullscreen mode Exit fullscreen mode

我想把它改成:

```
import { mod1, mod3 } from 'package1'
import * as mod2 from 'new-package'
import localMod from 'package2' 
```

Enter fullscreen mode Exit fullscreen mode

作为第一步，我尝试从导入的第一行中删除`mod2`，我成功地做到了，但是我不能删除`mod1`后的逗号。

到目前为止，我的代码片段如下所示: