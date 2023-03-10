# Python 字符串方法快速参考——查找和替换

> 原文：<https://dev.to/wangonya/a-quick-reference-to-python-string-methods-find-and-replace-15h5>

<small>注: <sup>*</sup> 代表必需参数。</small>

## 查找并替换

### `find()`

#### 参数

```
string.find(substring, start, end) 
```

Enter fullscreen mode Exit fullscreen mode

`substring` <sup>*</sup> :在`string`中要查找的子串。

`start`:开始搜索的索引。

`end`:结束搜索的索引。

#### 返回值

返回一个整数值，指向子字符串第一个匹配项的索引。如果没有找到子串，则返回`-1`。

#### 举例

```
Python 3.7.4

>>> s = "I find your lack of faith disturbing"

# find 'lack' in s
>>> s.find('lack')
12

# find 'vader' in s
>>> s.find('vader')
-1

# find 'lack' in s
# begin at index 13
>>> s.find('lack', 13)
-1

# find 'lack' in s
# begin at index 11
>>> s.find('lack', 11)
12

# find 'lack' in s
# begin at index 0, end at index 2
>>> s.find('lack', 0, 2)
-1

# find 'lack' in s
# begin at index 0, end at index 13
>>> s.find('lack', 0, 13)
12 
```

Enter fullscreen mode Exit fullscreen mode

### `rfind()`

#### 参数

```
string.rfind(substring, start, end) 
```

Enter fullscreen mode Exit fullscreen mode

`substring` <sup>*</sup> :在`string`中要查找的子串。

`start`:开始搜索的索引。

`end`:结束搜索的索引。

#### 返回值

返回一个整数值，指向子字符串出现处的最高索引**。如果没有找到子串，则返回`-1`。**

#### 举例

```
Python 3.7.4

>>> s = "Bond. James Bond."

# rfind 'Bond' in s
>>> s.rfind('Bond')
12

# rfind 'Herbert' in s
>>> s.rfind('Herbert')
-1

# rfind 'Bond' in s
# begin at index 13
>>> s.rfind('Bond', 13)
-1

# rfind 'Bond' in s
# begin at index 11
>>> s.rfind('Bond', 11)
12

# rfind 'Bond' in s
# begin at index 0, end at index 5
>>> s.rfind('Bond', 0, 5)
0

# rfind 'Bond' in s
# begin at index 0, end at index 16
>>> s.rfind('Bond', 0, 16)
12 
```

Enter fullscreen mode Exit fullscreen mode

### `index()`

#### 参数

```
string.index(substring, start, end) 
```

Enter fullscreen mode Exit fullscreen mode

`substring` <sup>*</sup> :在`string`中要查找的子串。

`start`:开始搜索的索引。

`end`:结束搜索的索引。

#### 返回值

返回一个整数值，指向子字符串第一个匹配项的索引。如果找不到子字符串，将引发 ValueError 异常。

#### 举例

```
Python 3.7.4

>>> s = "I find your lack of faith disturbing"

# index 'lack' in s
>>> s.index('lack')
12

# index 'vader' in s
>>> s.index('vader')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: substring not found

# index 'lack' in s
# begin at index 13
>>> s.index('lack', 13)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: substring not found

# index 'lack' in s
# begin at index 11
>>> s.index('lack', 11)
12

# index 'lack' in s
# begin at index 0, end at index 2
>>> s.index('lack', 0, 2)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: substring not found

# index 'lack' in s
# begin at index 0, end at index 13
>>> s.index('lack', 0, 13)
12 
```

Enter fullscreen mode Exit fullscreen mode

### `rindex()`

#### 参数

```
string.rindex(substring, start, end) 
```

Enter fullscreen mode Exit fullscreen mode

`substring` <sup>*</sup> :在`string`中要查找的子串。

`start`:开始搜索的索引。

`end`:结束搜索的索引。

#### 返回值

返回一个整数值，指向子字符串出现的最高索引。如果找不到子字符串，将引发 ValueError 异常。

#### 举例

```
Python 3.7.4

>>> s = "Bond. James Bond."

# rindex 'Bond' in s
>>> s.rindex('Bond')
12

# rindex 'Herbert' in s
>>> s.rindex('Herbert')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: substring not found

# rindex 'Bond' in s
# begin at index 13
>>> s.rindex('Bond', 13)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: substring not found

# rindex 'Bond' in s
# begin at index 11
>>> s.rindex('Bond', 11)
12

# rindex 'Bond' in s
# begin at index 0, end at index 5
>>> s.rindex('Bond', 0, 5)
0

# rindex 'Bond' in s
# begin at index 0, end at index 16
>>> s.rindex('Bond', 0, 16)
12 
```

Enter fullscreen mode Exit fullscreen mode

### `replace()`

#### 参数

```
string.replace(old, new, count) 
```

Enter fullscreen mode Exit fullscreen mode

`old` <sup>*</sup> :要替换的子串。

`new` <sup>*</sup> :要替换`old`的子串。

`count`:您想要用`new`替换的`old`的实例数。如果没有指定，`string`中`old`的所有**实例都将被替换。**

#### 返回值

返回一个`string`的**副本**，其中`old`的所有指定实例都被替换为`new`。

#### 举例

```
Python 3.7.4

>>> s = "My mother thanks you. My father thanks you. My sister thanks you. And I thank you."

# replace all instances of 'thank' with 'love'
>>> s.replace('thank', 'love')
'My mother loves you. My father loves you. My sister loves you. And I love you.'

# replace the first 2 instances of 'thank' with 'love
>>> s.replace('thank', 'love', 2)
'My mother loves you. My father loves you. My sister thanks you. And I thank you.' 
```

Enter fullscreen mode Exit fullscreen mode