# Python shebang

> 原文：<https://dev.to/wincentbalin/python-shebang-26jc>

目前，在 Python 2 和 Python 3 可能共存于同一系统的过渡期内，Python 程序中的 [shebang](https://en.wikipedia.org/wiki/Shebang_(Unix)) 行的 [PEP 0394 建议](https://www.python.org/dev/peps/pep-0394/#recommendation)如下所示:

1.  在 Python 解释器前使用`#!/usr/bin/env`
2.  对于既能使用 Python 2 又能使用*Python 3 的程序，只使用`#!/usr/bin/env python`*
**   如果你的程序只运行 Python 3，用`python3`替换前面一行中的`python`*   否则，对于基于 Python 2 的程序，用`python2`替换`python`*