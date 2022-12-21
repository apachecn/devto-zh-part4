# 一个小小的挑战:你能描述一下这段 PHP 代码是做什么的吗？

> 原文：<https://dev.to/felipperegazio/a-light-challenge-can-you-describe-what-this-php-code-does-1bgi>

我昨天写东西是为了好玩，我写了这个代码的和平。它执行一个简单而危险的任务(开玩笑，这并不危险，你可以在你的电脑上运行它，看看会发生什么)。

### 因此，挑战是:你能描述这段代码真正在做什么吗？你能照照它，描述尽可能多的步骤吗？

```
<?php $§=(rawurldecode('%24__%3D%5B%22%3D%22%2C%22s%22%2C%22T%22%2C%22K%22%2C%22f%22%2C%22R%22%2C%22C%22%2C%22K%22%2C%22r%22%2C%225%22%2C%22W%22%2C%22a%22%2C%22s%22%2C%225%22%2C%22W%22%2C%22d%22%5D%3B'));assert($§);$_=__FILE__;assert(base64_decode(strrev(implode($__)))); 
```

Enter fullscreen mode Exit fullscreen mode

可以使用 ctrl+c + v，也可以使用下面的字符串版本:

没什么大不了的，但也没那么容易。有经验的开发人员可能会很快找到答案，但我相信这对初学者来说会特别有趣。