# Python 字符串方法快速参考-案例

> 原文：<https://dev.to/wangonya/a-quick-reference-to-python-string-methods-cases-1nem>

## 例

### `capitalize()`

#### 参数

```
string.capitalize() 
```

Enter fullscreen mode Exit fullscreen mode

`capitalize()`不带参数。

#### 返回值

返回字符串的**副本**，第一个单词的第一个字母大写，字符串的所有其他字符小写。

#### 举例

```
Python 3.7.4

>>> s = "soMe peopLe AcTUally tyPE Like thIs."

>>> s.capitalize()
'Some people actually type like this.' 
```

Enter fullscreen mode Exit fullscreen mode

### `title()`

#### 参数

```
string.title() 
```

Enter fullscreen mode Exit fullscreen mode

`title()`不带参数。

#### 返回值

返回字符串的**副本**，其中**的第一个字母大写，每个**单词大写，字符串的所有其他字符小写。

#### 举例

```
Python 3.7.4

>>> s = "soMe peopLe AcTUally tyPE Like thIs."

>>> s.title()
'Some People Actually Type Like This.' 
```

Enter fullscreen mode Exit fullscreen mode

### `swapcase()`

#### 参数

```
string.swapcase() 
```

Enter fullscreen mode Exit fullscreen mode

`swapcase()`不带参数。

#### 返回值

返回字符串的一个**副本**，其中所有大写字符交换为小写字符，小写字符交换为大写字符。

#### 举例

```
Python 3.7.4

>>> s = "soMe peopLe AcTUally tyPE Like thIs."

>>> s.swapcase()
'SOmE PEOPlE aCtuALLY TYpe lIKE THiS.' 
```

Enter fullscreen mode Exit fullscreen mode

### `upper()`

#### 参数

```
string.upper() 
```

Enter fullscreen mode Exit fullscreen mode

`upper()`不带参数。

#### 返回值

返回字符串的一个**副本**，所有字符均为大写。

#### 举例

```
Python 3.7.4

>>> s = "soMe peopLe AcTUally tyPE Like thIs."

>>> s.upper()
'SOME PEOPLE ACTUALLY TYPE LIKE THIS.' 
```

Enter fullscreen mode Exit fullscreen mode

### `lower()`

#### 参数

```
string.lower() 
```

Enter fullscreen mode Exit fullscreen mode

`lower()`不带参数。

#### 返回值

返回字符串的一个**副本**，所有字符均为小写。

#### 举例

```
Python 3.7.4

>>> s = "soMe peopLe AcTUally tyPE Like thIs."

>>> s.lower()
'some people actually type like this.' 
```

Enter fullscreen mode Exit fullscreen mode