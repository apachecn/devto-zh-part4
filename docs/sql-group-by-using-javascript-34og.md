# 使用 JavaScript 进行 SQL 分组

> 原文：<https://dev.to/ketoaustin/sql-group-by-using-javascript-34og>

这篇文章探讨了 PostgreSQL 的`GROUP BY`子句和 JavaScript 的`reduce`方法如何能够帮助你根据属性对对象/数据行进行分组。我假设你已经熟悉了`GROUP BY`和`reduce`，并且你也(有点)有兴趣更深入地了解它们。

# PostgreSQL 的 GROUP BY 子句

> 根据 PostgreSQL 文档，“GROUP BY 子句用于将表中所有列中具有相同值的行组合在一起...其效果是将具有公共值的每组行组合成一个代表该组中所有行的组行。

下面回顾一下 PostreSQL 中的情况(*来源: [PostgreSQL 教程](http://www.postgresqltutorial.com/postgresql-group-by/)* ):

```
SELECT column_1, aggregate_function(column_2)
FROM tbl_name
GROUP BY column_1; 
```

Enter fullscreen mode Exit fullscreen mode

假设我们有一个`cities`表，其中包含一个具有`name`和`state`属性的城市列表，我们希望通过`state`来聚合这些城市。

*A list of United States cities and their states.*

<colgroup><col> <col></colgroup> 
| 名字 | 状态 |
| 圣安东尼奥 | 谢谢 |
| 亚特兰大 | 通用航空 |
| 奥斯汀 | 谢谢 |
| 纽约市 | 纽约州 |
| 休斯敦 | 谢谢 |

以下查询将把表中那些在`state`属性中具有相同值的行组合在一起。

```
SELECT state
FROM cities
GROUP BY state; 
```

Enter fullscreen mode Exit fullscreen mode

下面是查询结果的可视化表示。你可以看到在这种情况下`GROUP BY`充当了`SELECT DISTINCT`。换句话说，`GROUP BY`删除了表中所有重复的州。

*A list of all unique states in the cities table.*

<colgroup><col></colgroup> 
| 状态 |
| 通用航空 |
| 纽约州 |
| 谢谢 |

# 从数据中获得真知灼见

聚合函数对由`GROUP BY`创建的组进行操作，以帮助您回答有关数据的有趣问题。下面列出了一些聚合函数:(*来源: [PostgreSQL 文档](http://www.postgresqltutorial.com/postgresql-aggregate-functions/)* )。

*   AVG()–返回平均值。
*   COUNT()–返回值的个数。
*   MAX()–返回最大值。
*   MIN()–返回最小值。
*   SUM()–返回所有值或不同值的总和。

让我们使用`GROUP BY`和聚合函数来回答一些关于我们的数据的问题！

每个州有多少个城市？

```
SELECT state, COUNT(state)
FROM cities
GROUP BY state; 
```

Enter fullscreen mode Exit fullscreen mode*Each state along with the number of cities in that state.*

<colgroup><col> <col></colgroup> 
| 状态 | 数数 |
| 通用航空 | one |
| 纽约州 | one |
| 谢谢 | three |

哪个州拥有最多的城市？

```
SELECT state, COUNT(state)
FROM cities
GROUP BY state
ORDER BY count DESC
LIMIT 1 
```

Enter fullscreen mode Exit fullscreen mode*The state with the most cities.*

<colgroup><col> <col></colgroup> 
| 状态 | 数数 |
| 谢谢 | three |

# JavaScript 的 reduce 方法

如果您正在使用 JavaScript，并且有一组需要按特定属性分组的对象，该怎么办？好吧，让我们扩展上面的例子，假设我们有一个职员位置对象的数组，每个对象都有属性`name`、`city`和`state`。JavaScript 的`reduce`方法是解决这个问题的一种方法。

> 根据 MDN 文档，“reduce()方法对数组的每个元素执行一个 reducer 函数(您提供的),产生一个输出值。

您可以编写一个带有两个参数的函数:对象的数组和作为对象分组依据的属性。属性将根据它们的状态来表示您将`staffLocations`放入的“桶”。

下面的 reduce 方法采用以下参数:

*   **累加器** -存储每次调用回调函数时创建的返回值。当方法完成时，将返回该值(假设传入的数组不为空，在这种情况下将返回初始值)。
*   **对象** -这是数组中当前被操作的对象。
*   **回调** -这是你想要在数组中的每个对象上执行的函数。
*   **initialValue** -减速器功能第一次运行时，这将是累加器值。下面，`initialValue`就是`{}`。

```
const staffLocations = [
  { name: "Hannah", city: 'Houston', state: 'GA' },
  { name: "Ilhan",  city: 'Atlanta', state: 'GA' },
  { name: "Preet",  city: 'Houston', state: 'TX' },
  { name: "Adam",  city: 'Austin', state: 'TX' },
  { name: "Preston", city: 'New York City', state: 'NY' },
  { name: "Anna", city: 'Houston', state: 'TX' },
  { name: "Jakub",  city: 'Atlanta', state: 'GA' },
];

const groupBy = (objectArray, ...properties) => {
  return [...Object.values(objectArray.reduce((accumulator, object) => {
    const key = JSON.stringify(properties.map((x) => object[x] || null));

    if (!accumulator[key]) {
      accumulator[key] = [];
    }
    accumulator[key].push(object);
    return accumulator;
  }, {}))];
}

const groupedStaffLocations = groupBy(staffLocations, 'state');

groupedStaffLocations 
```

Enter fullscreen mode Exit fullscreen mode

`groupedStaffLocations`看起来像:

```
[
  [
    { name: "Preet", city: "Houston", state: "TX" },
    { name: "Adam", city: "Austin", state: "TX" },
    { name: "Anna", city: "Houston", state: "TX" },
  ],
  [
    { name: "Hannah", city: "Houston", state: "GA" },
    { name: "Ilhan", city: "Atlanta", state: "GA" },
    { name: "Jakub", city: "Atlanta", state: "GA" },
  ],
  [
    { name: "Preston", city: "New York City", state: "NY" },
  ]
] 
```

Enter fullscreen mode Exit fullscreen mode

回调步骤包括以下内容:

*   读取分组属性的值，并将其存储在`key`中。这象征着这个群体
*   如果累加器没有针对`key`中的值的现有组，则创建一个新组
*   将对象放入群组中

*来源: [MDN: Reduce:按属性分组对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce#Grouping_objects_by_a_property)T3】*

# 从数据中获得真知灼见

在您将数据简化为具有键值存储的信息桶之后，您可以映射相同的数据来回答有趣的问题，比如我们上面回答的问题:*“哪个州拥有最多的城市？”*。

```
const groupedCities = groupBy(cities, 'state');

// sort by length of array
let sortedArr = groupedCities.sort((a, b) => b.length - a.length);
// get the state of the first array, which would have the greatest length
sortedArr[0][0]['state'];

// returns:
// "TX" 
```

Enter fullscreen mode Exit fullscreen mode

# 多个属性

这个函数还支持按多个属性分组，所以它的工作方式类似于 SQL:
中的`GROUP BY`

```
const cityGroupedStaffLocations = groupBy(staffLocations, 'state', 'city'); 
```

Enter fullscreen mode Exit fullscreen mode

在本例中，`cityGroupedStaffLocations`返回代表居住在同一城市的员工的组:

```
[
  [
    { name: 'Hannah', city: 'Houston', state: 'GA' },
  ],
  [
    { name: 'Ilhan', city: 'Atlanta', state: 'GA' },
    { name: 'Jakub', city: 'Atlanta', state: 'GA' },
  ],
  [
    { name: 'Preet', city: 'Houston', state: 'TX' },
    { name: 'Anna', city: 'Houston', state: 'TX' },
  ],
  [
    { name: 'Adam', city: 'Austin', state: 'TX' },
  ],
  [
    { name: 'Preston', city: 'New York City', state: 'NY' },
  ]
] 
```

Enter fullscreen mode Exit fullscreen mode

这可以很容易地与`map`配对，以获得每个城市的员工人数:

```
cityGroupedStaffLocations.map(cityStaff => ({location: `${cityStaff[0].city}, ${cityStaff[0].state}`, numberOfStaff: cityStaff.length})) 
```

Enter fullscreen mode Exit fullscreen mode

返回:

```
[
  { location: 'Houston, GA', numberOfStaff: 1 },
  { location: 'Atlanta, GA', numberOfStaff: 2 },
  { location: 'Houston, TX', numberOfStaff: 2 },
  { location: 'Austin, TX', numberOfStaff: 1 },
  { location: 'New York City, NY', numberOfStaff: 1 },
] 
```

Enter fullscreen mode Exit fullscreen mode

# JSON.stringify？？？

```
const key = JSON.stringify(properties.flatMap((x) => object[x] || null)); 
```

Enter fullscreen mode Exit fullscreen mode

当通读`groupBy`方法时，您是否注意到`key`是 JSON？为了确保多个分组属性可以传递到函数中(`state`、`name`或`city`)，`key`必须是一个相应值的数组。在 JavaScript 中，`Object` s 只能使用字符串和符号作为键。将 group ( `key`)转换为 JSON 允许我们通过使用简单的 JSON 字符串比较来欺骗 JavaScript 缺乏深层结构相等性的问题。当组中的值转换为同一个 JSON 时，它们将被视为同一个组的一部分。虽然这可能会损害性能，但这是我在普通 JavaScript 中发现的使用数组作为键的最简洁的方法。

[![a cat saying 'cool'](img/08206be713b87d76ac7ede71d8213e80.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3u-1mM87--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codehannah.nyc/img/cool.svg)