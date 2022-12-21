# 每日挑战#1 -剥线器

> 原文：<https://dev.to/carolinagiorno/daily-challenge-1-string-peeler-14li>

我加入了#100 daysofcode 哈希标记组之后我发现了 Dev.to 的这个挑战并利用它来帮助我。
对于还不知道的人来说，这一挑战的岗位:

[![thepracticaldev image](img/9beda539d204a28e09d7b574a6cb63a3.png)](/thepracticaldev) [## 每日挑战#1 -剥线器

### 开发人员至员工 6 月 28 日 191 分钟阅读

#challenge](/thepracticaldev/daily-challenge-1-string-peeler-4nep)

我们走吧！

```
def stringpeeler(entrada):
    saida = entrada[1:len(entrada)-1]
    return saida 
```

因为我觉得挑战太简单了，所以我利用这个机会学习了一些 TDD，并与 pytest 库进行了互动。

```
# Dev.to Daily Challenge #1 - String Peeler
# https://dev.to/thepracticaldev/daily-challenge-1-string-peeler-4nep
# stringpeeler.py 
import pytest

def stringpeeler(entrada):
    if not isinstance(entrada, str):
        raise TypeError('A entrada deve ser uma string')
    if len(entrada)<3:
        return ValueError('A entrada deve ter no mínimo 3 caracteres')
    saida = entrada[1:len(entrada)-1]
    return saida 
```

和测试文件:

```
# Dev.to Daily Challenge #1 - String Peeler
# https://dev.to/thepracticaldev/daily-challenge-1-string-peeler-4nep import pytest
from stringpeeler import stringpeeler

#TDD Training NOT_STRING = 3524
EMPTY_STRING = ""
TOO_SHORT_STRING = "ab"
TEST_STRING = "zabcdey"

def test_ifString():
    with pytest.raises(TypeError):
        value = stringpeeler(NOT_STRING)

def test_emptyString():
    with pytest.raises(ValueError):
        value = stringpeeler(EMPTY_STRING)

def test_tooShortString():
    with pytest.raises(ValueError):
        value = stringpeeler(TOO_SHORT_STRING)

def test_stringPeeler():
    assert stringpeeler(TEST_STRING) == "abcde" 
```

我在 pytest 中引用了这一系列面向初学者的 TDD 帖子，这些帖子很有教育意义，很容易跟踪。

[![wangonya image](img/f24a24d0096b366df3522b3e07664d45.png)](/wangonya) [## 使用 Pytest 的 Python TDD 入门

### kinyanjui wango nya 1 月 28 日 192 分钟阅读

#python #tdd #tutorial #learning](/wangonya/python-tdd-with-pytest-----getting-started-4l07)

我不知道我能不能每天都做，但我现在玩得很开心。我希望在 TDD 坚持到最后的挑战！