# 加入符号学和 MassiveJS v6

> 原文：<https://dev.to/dmfay/join-semiotics-and-massivejs-v6-44he>

第六版即将推出，T2。下一个版本填补了大规模生成的 API 和日常 SQL 之间最大的差距，更不用说其他更高级别的数据访问库了。

对于 Massive 来说，这是一个逆转，直到现在，Massive 在同时处理多个数据库实体方面的功能非常有限。我甚至[将此作为一种并非没有好处的约束来写](https://dev.to/dmfay/centralize-your-query-logic-5bhh)(而且，为了记录在案，我认为临时连接是一种在应用程序代码中明智使用的工具！).

但是这种缺乏的主要原因是我从来没有想到过任何解决方案不适合笨拙的 options 对象。[深度插入](https://massivejs.org/docs/persistence#deep-insert)和[结果集分解](https://massivejs.org/docs/resultset-decomposition)就足够了。我自然不愿意承认构建模型的任何固有优势，但这看起来真的是很久以来的事情了。

然而，还是有办法的。如果我们侵犯一个虚拟图书馆系统的虚拟顾客的虚拟隐私，这就是大规模加入的样子

```
const whoCheckedOutCalvino = await db.libraries.join({
  books: {
    on: {library_id: 'id'},
    patron_books: {
      type: 'LEFT OUTER',
      pk: ['patron_id', 'book_id'],
      on: {book_id: 'books.id'},
      omit: true
    },
    who_checked_out: {
      type: 'LEFT OUTER',
      relation: 'patrons',
      on: {id: 'patron_books.patron_id'}
    }
  }
}).find({
  state: 'EV',
  'books.author ILIKE': 'calvino, %'
}); 
```

(`relation`在这个意义上表示一个表或视图。)

并且输出:

```
[{
  "id": 2,
  "name": "East Virginia State U",
  "state": "EV",
  "books": [{
    "author": "Calvino, Italo",
    "id": 1,
    "library_id": 2,
    "title": "Cosmicomics",
    "who_checked_out": [{
      "id": 1,
      "name": "Lauren Ipsum"
    }]
  }]
}, {
  "id": 3,
  "name": "Neitherfolk Public Library",
  "state": "EV",
  "books": [{
    "author": "Calvino, Italo",
    "id": 2,
    "library_id": 3,
    "title": "Cosmicomics",
    "who_checked_out": [{
      "id": 2,
      "name": "Daler S. Ahmet"
    }]
  }, {
    "author": "Calvino, Italo",
    "id": 4,
    "library_id": 3,
    "title": "Invisible Cities",
    "who_checked_out": []
  }]
}] 
```

或者换句话说，就是您希望它看起来是什么样子——如果您使用 Massive，您以前可能需要处理视图和分解模式来实现什么。这是一个相当复杂的例子，在缺省值(例如`type`到`INNER`)和自省之间，声明一个连接可以像命名目标一样简单:`db.libraries.join('books')`。

连接模式在某种程度上是分解模式的进化，共享相同的结构，但是推断出列列表、表主键，甚至一些存在明确外键关系的`on`条件。它更加简洁，不那么脆弱，并且仍然只在需要的时候和地方被精确地定义。更好的是，从表中创建的复合实体可以使用持久性方法，这意味着`join()`可以取代许多(如果不是大多数)深度插入和结果集分解的现有用法。

凭空创造出替代的数据库实体似乎有点不合常规。有一些先例——Massive 已经像对待数据库函数一样对待脚本——但是由`Readable.join()`创建的复合实体要复杂得多。这种疯狂是有方法的，它的起源可以追溯到 Ted Codd 提出关系数据库这个概念之前。

## 符号学来自三万英尺

简言之，符号学是对意义构建的研究，起源于 19 世纪的语言学和形式逻辑。这也是一个不断扩展的知识传统，与五花八门的其他不断扩展的知识传统进行对话，所以我不会在这里对它进行公正的评价。语言学方面的基本观点归功于弗迪南·德·索绪尔:意义是在一个*能指*与一个*所指*的关系中产生的，或者合在一起是一个*符号*。烟对火，字母对声音，等等。其他一切都源于这种关系。当然，还有很多其他的东西，就像很多其他的基础思想一样，索绪尔的二元结构是一件博物馆藏品。

但是用几乎代数的术语将意义本身理论化的想法会比索绪尔活得更久。逻辑学家查尔斯·桑德斯·皮尔士已经得出了类似的结论，并且意识到能指与所指的关系的解释价值和其他两个一样重要。皮尔士，沿着这条推理路线，理解这个“解释者”本身是一个符号，包含它自己的能指和所指，反过来在无限的意义链中产生它们自己的解释者。与此同时，路易·叶尔姆斯列夫将德·索绪尔的二元结构重新设想为表达*与内容*的关系*，并增加了形式*和物质*的第二维度*。对叶尔姆斯列夫来说，在数学意义上，符号是一种功能，将“表达形式”映射到“内容形式”，将构成符号的原材料命名为“表达的实质”和“内容的实质”。

术语“物质”的使用听起来有点像某种哲学术语，但这里没有技巧:它只是*东西*。没有比“物质”这样的词更具体的名称来表示“被制成符号的东西”；这一类别包括从物理材料到光、手势、定位、电等等的一切，有着无穷无尽的组合。符号是由这些物质被选择并形成内容和表达而产生的:燃料、氧气和热量被组织成火和烟，或者声音以一种与已知的语言量相对应的顺序发出。还应该说，意识不需要进入其中:任何东西都可以构成一个标志，甚至一株植物也可以解释一个标志。

这一切都是在说:有东西在那里，它的共同点是，它是有意义的事情。事实上，只要有一个解释的过程，大多数东西在同一时间总是有许多意思——而且总是有一些东西。由 Gilles Deleuze 和 Felix Guattari 组成的哲学家-心理学家 tag 团队将等待进一步形成的物质的原始汤设想为一个空间维度:一致性的*平面*或内在性的*平面*。正如他们在 *1000 Plateaus* 中所提出的，意义发生在一致性的平面之上，因为物质从中被选择和汲取，成为物质和符号。意义的递归性意味着这些符号被选择到其他符号的物质中，以一种他们比作沉积岩形成的方式成为平面上的层或地层。

## 标志和数据库

一个数据库管理系统，像任何其他程序一样，是一个极其复杂的符号系统。然而，使数据库管理系统(以及一些其他类别的软件，如分类账和版本控制系统)与众不同的是，它们被设计成管理其他符号系统。由于这种递归性，数据库可以被想象成一个一致性的平面，一个可以将无格式字节的任意组合绘制成列符号和行符号的空间，这些符号又可以聚集成表符号、视图符号和查询符号。

如果表、视图和查询仍然是基本的符号，那么差异到底在哪里呢？表存储持久数据，因此是可变的，而视图和查询不是，并且必须由表本身以及表之间的关系构成。表构成了符号的较低层次，视图将表和视图实体组成了较高层次的符号，而更高层次的查询离一致性平面足够远，它们不再存储在数据库本身中。

当然，这是绕了一大圈才到达继承的。从大方面来说，数据库实体是基类`Entity`的第一个实例，之后它们继承第二个原型:`Sequence`、`Executable`或`Readable`中的一个。后者中的一些也可以进一步称为`Writable`;没有不也是`Readable`的`Writable`

但是这里发生的事情不止一件，将表、视图和数据库函数排序成类层次是第二步——在它们可以形成标志之前，必须选择问题。在分层的第一步，Massive 将脚本文件添加到符号的 API 系统中，将它们(几乎)等同于函数和过程。

`Readable.join()`用同样的想法进一步扩展数据库的关系:以前，`Readable`与单个表或视图一一对应。但是只要可以生成适合的 SQL，没有理由一个`Readable`不能映射到多个关系。`Writable`对这件事来说也是:

```
const librariesWithBooks = db.libraries.join('books');
const libraryMembers = db.patrons.join('libraries');

// inserts work exactly like deep insert, persisting an
// entire object tree
const newLibrary = await librariesWithBooks.insert({
  name: 'Lichfield Public Library',
  state: 'EV',
  books: [{
    library_id: undefined,
    title: 'Jurgen: A Comedy of Justice',
    author: 'Cabell, James Branch'
  }, {
    library_id: undefined,
    title: 'If On a Winter\'s Night a Traveller',
    author: 'Calvino, Italo'
  }]
});

// updates make changes in the origin table, based on
// criteria which can reference the joined tables
const withCabell = await librariesWithBooks.update({
  'books.author ilike': 'cabell, %'
}, {
  has_cabell: true
});

// deletes, like updates, affect the origin table only
const iplPatrons = await libraryMembers.destroy({
  'libraries.name ilike': 'Imaginary Public Library'
}); 
```

## 试试看！

第一个 v6 预发布现已推出:`npm i massive@next`。现在文档中有一个[预发布部分详细介绍了新内容和不同之处。但是总结一下其他的变化:](https://massivejs.org/docs/prerelease)

*   不再支持低于 7.6 的节点。
*   隐式排序已被删除。
*   默认情况下，结果集分解现在生成数组而不是对象。`array`模式字段不再被识别，您需要将它从现有的分解模式中移除。要生成对象，请改为设置`decomposeTo: 'object'`。
*   JSON 和 JSONB 属性现在按照它们的原始类型排序，而不是作为文本处理。
*   `order`选项的`type`属性已经被弃用，取而代之的是其他地方使用的 Postgres 风格的`field::type`转换。它将在 6.x 的生命周期中继续工作，但在后续的主要版本中可能会被删除。

自从两年多前我第一次发表最初的 resultset 分解要点以来，这是我一直希望能够实现的一个特性。它涉及到对表加载、标准解析和语句生成的大量更改。我努力*而不是*打破这些领域，并通过将预发布版本放入现有代码库进行了非正式的实验。结果很好，但是如果你发现这个或任何其他大量功能的问题，请[让我知道](https://gitlab.com/dmfay/massive-js/issues)！

我真的很兴奋地看到连接在多大程度上扩展了 Massive 的功能，但事实上，只有一件事我认为我和大多数其他 Massive 用户将获得最大的收益:用 criteria 对象生成简单的旧查询谓词，而不必定义和管理过多的视图来覆盖基本的`JOIN` s。分层是一种考虑意义产生的有用方法-但分层本身也可能是一种负担。