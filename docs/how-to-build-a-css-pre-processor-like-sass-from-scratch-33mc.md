# 如何从头开始构建一个 CSS 预处理器(比如 SASS)

> 原文：<https://dev.to/abdurrahmaanj/how-to-build-a-css-pre-processor-like-sass-from-scratch-33mc>

如果你是做网页开发的，也许你听说过[萨斯](https://sass-lang.com/documentation)、[少](http://lesscss.org)、帕格、[铁笔](http://stylus-lang.com)等。这些都是前置处理器。在本教程中，我们将使用变量和函数从头开始构建一个功能强大的 css 预处理器。这种类型的新语言被称为源到源编译语言。如果你很激动，我希望不会让你失望。

# 绘制路线

首先让我们看看我们要做什么。我们将把这种语言称为带扩展名的点点。点

当设计一门新语言时，花些时间在设计上是有好处的。如果你有真正流动的东西，你可以开始编码。

下面是一个点点片段:

```
x = 1
y = 5
x-y-z = 6
col-z = berry

@f{
    border: 10px solid black;
    border-radius: 50%;
}

.add{
    color:white;
    background-color: rgb(3, 4, 5);
}

#ad{
    color: rgb(x, 4, 5);
}

.a div a{
    color: ..col-z;
    @f
} 
```

Enter fullscreen mode Exit fullscreen mode

编译成这样:

```
.add{
    color: white;
    background-color: rgb(3,4,5);
}

#ad{
    color: rgb(1,4,5);
}

.a div a{
    color: berry;
    border: 10px solid black;
    border-radius: 50%;
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/df3df9d83ebb9dd8f0e02304ae92ff20.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--J4rGKm7t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1547057740-4b18aac8eed2%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D500%26q%3D60)

# 解剖设计

让我们看看我们包括了哪些特性

### 变量

```
x = 1
y = 5
x-y-z = 6
col-z = berry 
```

Enter fullscreen mode Exit fullscreen mode

我们看到了

*   我们不需要在 Js 中指定像$或 var 这样的说明符。
*   我们可以在变量名中添加–

我们也可以在函数值中调用变量

```
rgb(x, 4, 5); 
```

Enter fullscreen mode Exit fullscreen mode

并且在属性值

```
color: ..col-z; 
```

Enter fullscreen mode Exit fullscreen mode

在哪里..直接表示属性中的变量调用。

### 功能

```
@f{
    border: 10px solid black;
    border-radius: 50%;
} 
```

Enter fullscreen mode Exit fullscreen mode

函数用@符号表示。在下面的例子中，这些被称为扩展到它所包含的属性。

```
.a div a{
    color: ..col-z;
    @f
} 
```

Enter fullscreen mode Exit fullscreen mode

这已经够复杂的了。开始吧！

```
import copy # we have just one import 
```

Enter fullscreen mode Exit fullscreen mode

我们把源作为一个变量

```
source = '''

x = 1
y = 5
x-y-z = 6
col-z = berry

@f{
    border: 10px solid black;
    border-radius: 50%;
}

.add{
    color:white;
    background-color: rgb(3, 4, 5);
}

#ad{
    color: rgb(x, 4, 5);
}

.a div a{
    color: ..col-z;
    @f
}
''' 
```

Enter fullscreen mode Exit fullscreen mode

# 定义常数

我们已经将常量捆绑在一个类中。

```
class SYM:
    LEFT_BRACE = '{'
    RIGHT_BRACE = '}'
    LEFT_ROUND = '('
    RIGHT_ROUND = ')'
    NEW_LINE = '\n'
    TAB = '    '
    COLON = ':'
    SEMI_COLON = ';'
    SPACE = ' '
    COMMA = ','
    EQUAL = '='
    UNION = '-'
    DOT = '.'
    AT = '@' 
```

Enter fullscreen mode Exit fullscreen mode

然后我们定义我们的关键字

```
KEYWORDS = (SYM.LEFT_BRACE, SYM.RIGHT_BRACE, SYM.NEW_LINE, SYM.TAB, SYM.COLON, 
    SYM.SEMI_COLON, SYM.SPACE, SYM.RIGHT_ROUND, SYM.LEFT_ROUND, SYM.COMMA, SYM.EQUAL) 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/18b71599e65eba6c8acbc70e8bbf91b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mSRUlJtL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/reserve/81gZijLSWfge41LgzqQ6_Moving%2520Parts.JPG%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D752%26q%3D80)

## 建造 Lexer

lexer 的代码来自本 lexer 教程。如果你觉得有必要，请过目一下。这里我们用 get_lexeme 方法将代码转换成一个类。方法给了我们一个列表。上面的点点片段被转换成下面的 lexemes 变量中的列表。

下面是我们的 lexer 类:

```
class Lexer:
    def __init__(self, source: str, KEYWORDS: list):
        self.white_space = SYM.SPACE
        self.KEYWORDS = KEYWORDS
        self.lexeme = ''
        self.lexemes = []
        self.string = source

    def get_lexemes(self) -> list:
        for i, char in enumerate(self.string):
            if char != self.white_space and char != SYM.NEW_LINE:
                self.lexeme += char  # adding a char each time
            if (i+1 &lt; len(self.string)):  # prevents error
                if self.string[i+1] == self.white_space or self.string[i+1] in KEYWORDS or self.lexeme in KEYWORDS or self.string[i+1] == SYM.NEW_LINE: # if next char == ' '
                    if self.lexeme != '':
                        self.lexemes.append(self.lexeme)
                        self.lexeme = ''
        return self.lexemes 
```

Enter fullscreen mode Exit fullscreen mode

然后我们声明我们的基本变量:

```
v = Lexer(source, KEYWORDS)
lexemes = v.get_lexemes()

lexemes.append('') # appending an unused last element to properly dump all values 
```

Enter fullscreen mode Exit fullscreen mode

词汇现在等于

```
['x', '=', '1', 'y', '=', '5', 'x-y-z', '=', '6', 'col-z', 
'=', 'berry', '@f', '{', 'border', ':', '10px', 'solid', 
'black', ';', 'border-radius', ':', '50%', ';', '}', '.add', 
'{', 'color', ':', 'white', ';', 'background-color', ':', 
'rgb', '(', '3', ',', '4', ',', '5', ')', ';', '}', '#ad', 
'{', 'color', ':', 'rgb','(', 'x', ',', '4', ',', '5', ')', 
';', '}', '.a', 'div', 'a', '{', 'color', ':', '..col-z', 
';', '@f', '}', ''] 
```

Enter fullscreen mode Exit fullscreen mode

有了这样分离的数据，前进就容易多了！

# 记忆的概念

接下来我们定义一个字典来保存我们所有的变量。

```
memory = {} 
```

Enter fullscreen mode Exit fullscreen mode

其中

```
x = 1 
```

Enter fullscreen mode Exit fullscreen mode

以后会变成这样:

```
{'x':'1'} 
```

Enter fullscreen mode Exit fullscreen mode

要检索它，我们只需做

```
memory['x'] 
```

Enter fullscreen mode Exit fullscreen mode

## 树的概念

我们将有一本名为树的字典

```
tree = {} 
```

Enter fullscreen mode Exit fullscreen mode

它将把转换后的代码保存为

```
{
    '@f': {
        'border': '10px solid black', 
        'border-radius': '50%'
    }, 
    '.add': {
        'color':'white', 
        'background-color': 'rgb ( 3 , 4 , 5 )'
    }, '#ad': {
        'color': 'rgb ( x ,4 , 5 )'
    }, 
    '.a div a': {
        'color': '..col-z', 
        '@f': ''
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的下一步就是:将词汇列表转换成字典

[![](img/a1363d976eee516120fe8facabf0c685.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iw7QrQmK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1445294211564-3ca59d999abd%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D667%26q%3D80)

# 生成树

为了跟踪我们所处的位置，我们将有一系列取真/假值(开/关)的变量

### 设置持有者

```
id_string = ''
last_id_string = ''
last_attribute = ''

current_attribute = ''
current_value = ''

len_lexemes = len(lexemes) 
```

Enter fullscreen mode Exit fullscreen mode

id 字符串将像#add，.x 一个部门等

last_ variables 只保存在离开子部分时没有被清空的变量

### 设置标志

我们会有三面旗子。

一个是当我们开始一个程序块时，当遇到一个{时，它将变为真，当遇到一个}时，它将变为假

一个属性是颜色中的颜色:黑色；

当经过{和}时，属性“正在进行”将变为真；并且当越过时将变为假:

value _ continuous 走过去会变成 true 走过去会变成 false

```
block_ongoing = False
attribute_ongoing = False
value_ongoing = False 
```

Enter fullscreen mode Exit fullscreen mode

我们将开始遍历列表，并实现我们在上面描述的标志

```
for i, lex in enumerate(lexemes):

    if i+1 &lt; len_lexemes:
        next_lexeme = lexemes[i+1]
    prev_lexeme = lexemes[i-1]

    if lex == SYM.LEFT_BRACE:
        block_ongoing = True
        attribute_ongoing = True
        continue
    elif lex == SYM.RIGHT_BRACE:
        block_ongoing = False
        statement_ongoing = False
        attribute_ongoing = False
        value_ongoing = False
        continue
    if lex == SYM.COLON:
        value_ongoing = True
        attribute_ongoing = False
        continue
    elif lex == SYM.SEMI_COLON:
        value_ongoing = False
        statement_ongoing = False
        attribute_ongoing = True
        continue 
```

Enter fullscreen mode Exit fullscreen mode

继续是必要的，因为一旦我们激活了一个标志，我们没有工作要做，我们继续前进。

# 处理变量

要给变量赋值，我们只需等待=号，然后继续。

然后，当检查变量名或值时，我们只需使用 continue
来防止循环继续

```
 if lex == SYM.EQUAL:
        memory[prev_lexeme] = next_lexeme
        continue
    elif next_lexeme == SYM.EQUAL or prev_lexeme == SYM.EQUAL:
        continue 
```

Enter fullscreen mode Exit fullscreen mode

# 树建在哪里

现在我们将使用标志

```
 if not block_ongoing:
        id_string += lex + ' '
    elif block_ongoing:
        if id_string:
            tree[id_string.strip()] = {}
            last_id_string = id_string.strip()
            id_string = '' 
```

Enter fullscreen mode Exit fullscreen mode

这里我们处理的是块 id，例如#add {}中的#add。我们做了

```
tree[id_string.strip()] = {} 
```

Enter fullscreen mode Exit fullscreen mode

这里是一个示例树

```
{
'.add': {}
} 
```

Enter fullscreen mode Exit fullscreen mode

使用同样的原理，我们将对属性
也这样做

```
 if attribute_ongoing:
        current_attribute += lex
    elif not attribute_ongoing:
        if current_attribute:
            tree[last_id_string][current_attribute] = ''
            last_attribute = current_attribute
            current_attribute = '' 
```

Enter fullscreen mode Exit fullscreen mode

这里是一个示例树

```
{
'.add': {
        'color':''
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

和值

```
 if value_ongoing:
        current_value += lex + ' '
    elif not value_ongoing:
        if current_value:
            tree[last_id_string][last_attribute] = current_value.strip()
            last_value = current_value.strip()
            current_value = '' 
```

Enter fullscreen mode Exit fullscreen mode

这里是一个示例树

```
{
'.add': {
        'color':'white'
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个片段结束了我们的树构建模块

# 替换数值

现在让我们看看如何替换函数中的变量名，如 rgb()和 rgba()

```
def parseFunc(f):
    v = f.split(SYM.LEFT_ROUND)
    name = v[0]
    vals = v[1][:-1].replace(SYM.SPACE, '')
    values = vals.split(SYM.COMMA)
    for i, v in enumerate(values):
        if not v.isnumeric():
            values[i] = memory[v]
    return '{}({})'.format(name.strip(), ','.join(values)) 
```

Enter fullscreen mode Exit fullscreen mode

这里我们通过在内存字典中将 x 替换为 1 来将 rgb(x，4，5)替换为 rgb(1，4，5)。为了从头开始构建一个 CSS 预处理器，我们必须接受所有的情况，这将在后面进行。

[![](img/4256358f4f8a8138fa6dc78ea2710241.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WhIAWYcw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1507608616759-54f48f0af0ee%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D334%26q%3D80)

# 将树转换成它的最终形状

现在我们需要再次遍历树，扩展函数，替换值，包括使用上面定义的函数。

从技术上讲，我们不能在迭代的时候改变字典。我们必须复制它来打破引用。我们必须复制它。

```
ntree = copy.deepcopy(tree) 
```

Enter fullscreen mode Exit fullscreen mode

然后我们迭代:

```
for block_name in ntree:
    properties = ntree[block_name]
    if block_name[0] == SYM.AT:
        continue 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们将 id 的第一个符号作为@，我们只需跳过它，因为我们不需要扩展定义。

```
 for element in properties:
        value = properties[element]
        if SYM.LEFT_ROUND in value:
            tree[block_name][element] = parseFunc(value) 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们说如果值中有一个(它表示像 rgb()这样的函数。在这种情况下，我们使用函数 parseFunc。

```
 if SYM.DOT in value:
            tree[block_name][element] = memory[value.strip(SYM.DOT)] 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们看到一个 a。在这个值中，我们在内存字典中查找并替换它

```
 if SYM.AT in element:
            del tree[block_name][element]
            tree[block_name].update(tree[element]) 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将@符号视为块中的键，我们只需将其中的字典添加到该块中的字典中(由 Python 中的 update()完成)。

这也显示了使用数据结构优于普通字符串的优势。

[![](img/359eb80d4dbaa2aada4fce1ec86e40ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N8bt1Xys--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1549049961-dc5c55b76754%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D400%26q%3D80)

# 编译为普通 Css

现在我们可以把它编译成普通的 CSS，我们现在只打印它(你可以在打印时顺便说一下)

```
for key in tree:
    if key[0] == SYM.AT:
        continue
    print(key, '{', sep='')
    for elem in tree[key]:
        print('{}{}: {};'.format(SYM.TAB, elem, tree[key][elem]))
    print('}\n') 
```

Enter fullscreen mode Exit fullscreen mode

从而产生

```
.add{
    color: white;
    background-color: rgb(3,4,5);
}

#ad{
    color: rgb(1,4,5);
}

.a div a{
    color: berry;
    border: 10px solid black;
    border-radius: 50%;
} 
```

Enter fullscreen mode Exit fullscreen mode

# It 的未来

这是在没有安全网的情况下完成的，并且缺少很多很酷的特性，但是这样做是为了证明，使用一些正常的编程逻辑，您可以设置一些很酷的东西。这是我每周常用的 linkedIn Python 项目。

如果你想基于缩进(像手写笔一样)从头构建一个 CSS 预处理器，使用[本教程](https://www.pythonmembers.club/2018/05/04/building-an-indentation-analyser-in-python-tutorial/)。

下一个要补充的是什么？下面评论一下！

Github 链接:[https://github.com/Abdur-rahmaanJ/dotdot](https://github.com/Abdur-rahmaanJ/dotdot)

镜像于 [PythonMembersClub](https://www.pythonmembers.club/2019/07/18/dsl-python-new-language-how-to-build-a-css-pre-processor-like-sass-from-scratch-dotdot/)

图片来自 unsplash

阿卜杜勒·拉赫曼·扬汉吉尔