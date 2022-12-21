# Python 字符串方法快速参考——分割和切片

> 原文：<https://dev.to/wangonya/a-quick-reference-to-python-string-methods-splitting-and-slicing-2d9o>

<small>注: <sup>*</sup> 代表必需参数。</small>

## 分割和切片

### `join()`

#### 参数

```
string.join(iterable) 
```

Enter fullscreen mode Exit fullscreen mode

`iterable`<sup>*</sup>:python 可迭代的，例如列表、元组、字符串、字典或集合。

#### 返回值

返回一个字符串，该字符串与 iterable 中传递的元素连接在一起。

#### 举例

```
Python 3.7.4

>>> n1 = ['1', '2', '3', '4', '5']
>>> n2 = ('1', '2', '3', '4', '5')
>>> separator = ', '

>>> separator.join(n1)
'1, 2, 3, 4, 5'
>>> separator.join(n2)
'1, 2, 3, 4, 5' 
```

Enter fullscreen mode Exit fullscreen mode

### `partition()`

#### 参数

```
string.partition(separator) 
```

Enter fullscreen mode Exit fullscreen mode

`separator` <sup>*</sup> :字符串中触发分离的部分。如果它在字符串中出现多次，则认为是第一次出现。

#### 返回值

返回包含三个部分的元组:

*   分隔符之前的字符串部分
*   分离器本身
*   分隔符之后的字符串部分

#### 举例

```
Python 3.7.4

>>> s = "I feel the need - the need for speed!"
>>> s.partition("-")
('I feel the need ', '-', ' the need for speed!')

>>> s.partition(",")
('I feel the need - the need for speed!', '', '')

>>> s = "I feel the need - the need - for speed!"
>>> s.partition("-")
('I feel the need ', '-', ' the need - for speed!') 
```

Enter fullscreen mode Exit fullscreen mode

### `rpartition()`

#### 参数

```
string.rpartition(separator) 
```

Enter fullscreen mode Exit fullscreen mode

`separator` <sup>*</sup> :字符串中触发分离的部分。如果它在字符串中出现多次，则考虑最后一次出现。

#### 返回值

返回包含三个部分的元组:

*   分隔符最后一次出现之前的字符串部分
*   分离器本身
*   分隔符之后的字符串部分

#### 举例

```
Python 3.7.4

>>> s = "I feel the need - the need for speed!"
>>> s.rpartition("-")
('I feel the need ', '-', ' the need for speed!')

>>> s.rpartition(",")
('', '', 'I feel the need - the need for speed!')

>>> s = "I feel the need - the need - for speed!"
>>> s.rpartition("-")
('I feel the need - the need ', '-', ' for speed!') 
```

Enter fullscreen mode Exit fullscreen mode

### `split()`

#### 参数

```
string.split(separator, maxsplit) 
```

Enter fullscreen mode Exit fullscreen mode

`separator`:用于分隔字符串的元素。如果未指定，则使用空格分隔字符串。

`maxsplit`:最大分割数

#### 返回值

返回在指定的`separator`处分隔的字符串列表。

#### 举例

```
Python 3.7.4

>>> s = "I feel the need - the need for speed!"
>>> s.split()
['I', 'feel', 'the', 'need', '-', 'the', 'need', 'for', 'speed!']

>>> s.split("-")
['I feel the need ', ' the need for speed!']

>>> s.split("need")
['I feel the ', ' - the ', ' for speed!']

>>> s.split("need", 1)
['I feel the ', ' - the need for speed!'] 
```

Enter fullscreen mode Exit fullscreen mode

### `rsplit()`

#### 参数

```
string.rsplit(separator, maxsplit) 
```

Enter fullscreen mode Exit fullscreen mode

`separator`:用于分隔字符串的元素。如果未指定，则使用空格分隔字符串。

`maxsplit`:最大分割数

#### 返回值

返回从右侧开始在指定的`separator` **处分隔的字符串列表。**

#### 举例

```
Python 3.7.4

>>> s = "I feel the need - the need for speed!"
>>> s.rsplit()
['I', 'feel', 'the', 'need', '-', 'the', 'need', 'for', 'speed!']

>>> s.rsplit("-")
['I feel the need ', ' the need for speed!']

>>> s.rsplit("need")
['I feel the ', ' - the ', ' for speed!']

>>> s.rsplit("need", 1)
['I feel the need - the ', ' for speed!'] 
```

Enter fullscreen mode Exit fullscreen mode

### `splitlines()`

#### 参数

```
string.splitlines(keepends) 
```

Enter fullscreen mode Exit fullscreen mode

`keepends`:如果包含，换行符也包含在列表的返回项中。作为`True`或`False`提供。如果没有提供，默认为`False`。

#### 返回值

返回字符串中的行列表。

#### 举例

```
Python 3.7.4

>>> s = """“Hope” is the thing with feathers -
... That perches in the soul -
... And sings the tune without the words -
... And never stops - at all -"""

>>> s.splitlines()
['“Hope” is the thing with feathers -', 'That perches in the soul -', 
'And sings the tune without the words -', 'And never stops - at all -']

>>> s.splitlines(True)
['“Hope” is the thing with feathers -\n', 'That perches in the soul -\n', 
'And sings the tune without the words -\n', 'And never stops - at all -'] 
```

Enter fullscreen mode Exit fullscreen mode