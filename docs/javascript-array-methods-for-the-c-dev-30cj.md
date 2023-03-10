# 用于 C#开发的 JavaScript 数组方法

> 原文：<https://dev.to/sparky/javascript-array-methods-for-the-c-dev-30cj>

不久前，我在办公室给一群 C#开发人员做了一个 PowerPoint 知识分享演示，讲的是 JavaScript 数组方法以及它们与。我们熟悉的. NET LINQ 方法。

从那以后，我发现自己一直在引用那个演示文稿，所以我想我应该把它放在我和其他人可以访问的地方，以便将来参考。

下面的标题是这些方法的 MDN 文档的链接，因此您可以在那里找到更多的细节和浏览器兼容性信息。

(这些并不是所有的数组方法，只是“LINQ-y”的方法。)

### [数组长度](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/length)

*   目的:获取数组大小
*   退货:数量
*   LINQ 等价:[IEnumerable<T1】TT7】。数](https://docs.microsoft.com/en-us/dotnet/api/system.linq.enumerable.count?view=netframework-4.8)(或[阵。长度](https://docs.microsoft.com/en-us/dotnet/api/system.array.length?view=netframework-4.8)

```
const months = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'];

console.log(`There are ${months.length} months in your earth year`); 
```

Enter fullscreen mode Exit fullscreen mode

```
There are 12 months in your earth year 
```

Enter fullscreen mode Exit fullscreen mode

### [array.forEach](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)

*   目的:对数组中的每一项做一些事情
*   自变量:项，(*索引*)，(*列表*)
*   “回调”责任:对项目执行某些操作
*   返回:无(未定义)
*   C#/LINQ 等价:[列表<T1】TT5】。ForEach](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.list-1.foreach?view=netframework-4.8)

```
const year = new Date().getFullYear();

const getDaysInMonth = month =>
  new Date(year, new Date(`1-${month}-${year}`).getMonth() + 1, 0).getDate();

months.forEach(m => console.log(`${m} has ${getDaysInMonth(m)} days in ${year}`)); 
```

Enter fullscreen mode Exit fullscreen mode

```
Jan has 31 days in 2019
Feb has 28 days in 2019
Mar has 31 days in 2019
Apr has 30 days in 2019
May has 31 days in 2019
Jun has 30 days in 2019
Jul has 31 days in 2019
Aug has 31 days in 2019
Sep has 30 days in 2019
Oct has 31 days in 2019
Nov has 30 days in 2019
Dec has 31 days in 2019 
```

Enter fullscreen mode Exit fullscreen mode

forEach 和下面讨论的许多其他函数需要一个“item”参数，但也接受可选的“index”和“list”参数。

下面是一个 **forEach(item，index)** :
的例子

```
months.forEach((m, i) => console.log(`${m} is month #${i + 1}`)); 
```

Enter fullscreen mode Exit fullscreen mode

```
Jan is month #1
Feb is month #2
Mar is month #3
Apr is month #4
May is month #5
Jun is month #6
Jul is month #7
Aug is month #8
Sep is month #9
Oct is month #10
Nov is month #11
Dec is month #12 
```

Enter fullscreen mode Exit fullscreen mode

…和 **forEach(项目，索引，列表)** :

```
months.forEach((m, i, list) => console.log(`${i + 1}) ${m} follows ${list.slice(0, i)}`)); 
```

Enter fullscreen mode Exit fullscreen mode

```
1) Jan follows
2) Feb follows Jan
3) Mar follows Jan,Feb
4) Apr follows Jan,Feb,Mar
5) May follows Jan,Feb,Mar,Apr
6) Jun follows Jan,Feb,Mar,Apr,May
7) Jul follows Jan,Feb,Mar,Apr,May,Jun
8) Aug follows Jan,Feb,Mar,Apr,May,Jun,Jul
9) Sep follows Jan,Feb,Mar,Apr,May,Jun,Jul,Aug
10) Oct follows Jan,Feb,Mar,Apr,May,Jun,Jul,Aug,Sep
11) Nov follows Jan,Feb,Mar,Apr,May,Jun,Jul,Aug,Sep,Oct
12) Dec follows Jan,Feb,Mar,Apr,May,Jun,Jul,Aug,Sep,Oct,Nov 
```

Enter fullscreen mode Exit fullscreen mode

我将只展示其余函数的 *fn* (item)示例…

### [array.map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)

*   目的:将数组映射到一个新数组(相同或不同类型)
*   自变量:项，(*索引*)，(*列表*)
*   “回调”责任:为新数组返回新项目
*   返回:新数组
*   LINQ 等价:[IEnumerable<T1T>。选择](https://docs.microsoft.com/en-us/dotnet/api/system.linq.enumerable.select?view=netframework-4.8)

```
const firstDaysOfMonths = months.map(m => new Date(`1-${m}-${year}`));

firstDaysOfMonths.forEach(item => console.log(item)); 
```

Enter fullscreen mode Exit fullscreen mode

```
Mon Jan 01 2018 00:00:00 GMT-0600 (Central Standard Time)
Thu Feb 01 2018 00:00:00 GMT-0600 (Central Standard Time)
Thu Mar 01 2018 00:00:00 GMT-0600 (Central Standard Time)
Sun Apr 01 2018 00:00:00 GMT-0500 (Central Daylight Time)
Tue May 01 2018 00:00:00 GMT-0500 (Central Daylight Time)
Fri Jun 01 2018 00:00:00 GMT-0500 (Central Daylight Time)
Sun Jul 01 2018 00:00:00 GMT-0500 (Central Daylight Time)
Wed Aug 01 2018 00:00:00 GMT-0500 (Central Daylight Time)
Sat Sep 01 2018 00:00:00 GMT-0500 (Central Daylight Time)
Mon Oct 01 2018 00:00:00 GMT-0500 (Central Daylight Time)
Thu Nov 01 2018 00:00:00 GMT-0500 (Central Daylight Time)
Sat Dec 01 2018 00:00:00 GMT-0600 (Central Standard Time) 
```

Enter fullscreen mode Exit fullscreen mode

### 

*   目的:创建一个匹配谓词的新项目数组
*   自变量:项，(*索引*)，(*列表*)
*   “回调”责任:谓词-返回项目的“真/假”值
*   返回:新的筛选数组
*   LINQ 等价:[IEnumerable<T1T>。其中](https://docs.microsoft.com/en-us/dotnet/api/system.linq.enumerable.where?view=netframework-4.8)

```
const monthsWith30Days = months.filter(m => getDaysInMonth(m) === 30);

console.log(monthsWith30Days); 
```

Enter fullscreen mode Exit fullscreen mode

```
Apr,Jun,Sep,Nov 
```

Enter fullscreen mode Exit fullscreen mode

### [array.reduce](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)

*   目的:根据数组项确定某个值(如总和、最大值)
*   自变量:结果、项、(*索引*)、(*列表*)
*   “回调”责任:基于先前的结果，什么应该传递给下一个迭代？
*   返回:上次迭代的结果
*   LINQ 等价物:有点像 [IEnumerable。聚合< *T* >](https://docs.microsoft.com/en-us/dotnet/api/system.linq.enumerable.aggregate?view=netframework-4.8)

```
const daysInYear = months.reduce((result, m) => result + getDaysInMonth(m), /*initial value:*/ 0);

console.log(`There are ${daysInYear} days in ${year}`); 
```

Enter fullscreen mode Exit fullscreen mode

```
There are 365 days in 2018 
```

Enter fullscreen mode Exit fullscreen mode

### [array . reduce right](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/ReduceRight)

…类似于 reduce，但它从“从右到左”处理项目。当我最初展示这些材料时，我想不出 reduceRight 的好用法，但我在凯尔·舍夫林的 JavaScript 课中看到了一个 egghead.io [用函数组合构建复杂函数的例子，展示了它的一个漂亮用法:](https://egghead.io/lessons/javascript-build-complex-functions-with-function-composition-in-javascript) 

```
const shout = message => message.toUpperCase();
const exclaim = message => message + '!';
const repeat = message => `${message}  ${message}`;

console.log(shout(repeat(exclaim('nested functions'))));

const compose = (...funcs) => x => funcs.reduceRight((result, func) => func(result), x);

const makeKnown = compose(
  shout,
  repeat,
  exclaim
);

console.log(makeKnown('composed function')); 
```

Enter fullscreen mode Exit fullscreen mode

```
NESTED FUNCTIONS! NESTED FUNCTIONS!
COMPOSED FUNCTION! COMPOSED FUNCTION! 
```

Enter fullscreen mode Exit fullscreen mode

### [array.some](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some)

*   目的:是否有一个或多个条目匹配一个谓词？
*   自变量:项，(*索引*)，(*列表*)
*   “回调”责任:谓词-返回项目的“真/假”值
*   如果有任何“真”响应，则返回:true(在第一个“真”响应后停止迭代)
*   LINQ 等价:[IEnumerable<T1T>。任何](https://docs.microsoft.com/en-us/dotnet/api/system.linq.enumerable.any?view=netframework-4.8)

```
const hasMonthStartingWithA = months.some(m => {
  console.log(`checking ${m}...`);
  return m[0] === 'A';
});

console.log(hasMonthStartingWithA); 
```

Enter fullscreen mode Exit fullscreen mode

```
checking Jan...
checking Feb...
checking Mar...
checking Apr...
true 
```

Enter fullscreen mode Exit fullscreen mode

*   目的:所有项目都匹配一个谓词吗？
*   自变量:项，(*索引*)，(*列表*)
*   “回调”责任:谓词-返回项目的“真/假”值
*   返回:如果所有响应都是“真”(在“假”响应后停止迭代)
*   LINQ 等价:[IEnumerable<T1T>。全部](https://docs.microsoft.com/en-us/dotnet/api/system.linq.enumerable.all?view=netframework-4.8)

```
const hasNo30DayMonths = months.every(m => {
  const daysInMonth = getDaysInMonth(m);
  console.log(`${m} has ${daysInMonth} days`);
  return daysInMonth != 30;
});

console.log(hasNo30DayMonths); 
```

Enter fullscreen mode Exit fullscreen mode

```
Jan has 31 days
Feb has 28 days
Mar has 31 days
Apr has 30 days
false 
```

Enter fullscreen mode Exit fullscreen mode

### 

*   目的:查找匹配谓词的第一个项目
*   自变量:项，(*索引*)，(*列表*)
*   “回调”责任:谓词-返回项目的“真/假”值
*   返回第一个“真值”项(或未定义，如果没有找到—在第一个“真值响应”后停止迭代)
*   LINQ 等价:[IEnumerable<T1T>。第一顺序默认](https://docs.microsoft.com/en-us/dotnet/api/system.linq.enumerable.firstordefault?view=netframework-4.8)

```
const first30DayMonth = months.find(m => getDaysInMonth(m) === 30);
const first40DayMonth = months.find(m => getDaysInMonth(m) === 40);
console.log(`1st 30 day month: ${first30DayMonth}`);
console.log(`1st 40 day month: ${first40DayMonth}`); 
```

Enter fullscreen mode Exit fullscreen mode

```
1st 30 day month: Apr
1st 40 day month: undefined 
```

Enter fullscreen mode Exit fullscreen mode

### [array.findIndex](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/findIndex)

…类似于 find，但它返回找到的索引，而不是项目(如果没有找到，则返回-1):

```
const index30 = months.findIndex(m => getDaysInMonth(m) === 30);
const index40 = months.findIndex(m => getDaysInMonth(m) === 40);
console.log(`1st 30 day index: ${index30}`);
console.log(`1st 40 day index: ${index40}`); 
```

Enter fullscreen mode Exit fullscreen mode

```
1st 30 day index: 3
1st 40 day index: -1 
```

Enter fullscreen mode Exit fullscreen mode

*   目的:数组是否包含指定的条目？
*   返回:布尔值
*   LINQ 等价:[IEnumerable<T1T>。包含](https://docs.microsoft.com/en-us/dotnet/api/system.linq.enumerable.contains?view=netframework-4.8)

```
console.log(months.includes('Aug'));
console.log(months.includes('Dog')); 
```

Enter fullscreen mode Exit fullscreen mode

```
true
false 
```

Enter fullscreen mode Exit fullscreen mode

*   目的:排序数组项目
*   参数:(compare function)-可选回调函数，接受 item1 和 item2 并返回:

    *   如果项目 1 >项目 2，则为负数
    *   如果项目 1 ==项目 2，则为零
    *   如果项目 2 >项目 1，则为正数

    (如果省略 compareFunction，则执行 unicode 比较，这意味着所有内容都被视为字符串)
    返回:排序后的数组(不是新的排序后的数组—它改变了源数组！)

*   LINQ 等价:[IEnumerable<T1T>。排序依据](https://docs.microsoft.com/en-us/dotnet/api/system.linq.enumerable.orderby?view=netframework-4.8)

这是一个更老的 JavaScript 函数，在“好的部分”更少的时候。

注意，months.sort()调用更新了 months 变量的内容:

```
 console.log(months);
  console.log(months.sort());
  console.log(months); 
```

Enter fullscreen mode Exit fullscreen mode

```
 Jan,Feb,Mar,Apr,May,Jun,Jul,Aug,Sep,Oct,Nov,Dec
  Apr,Aug,Dec,Feb,Jan,Jul,Jun,Mar,May,Nov,Oct,Sep
  Apr,Aug,Dec,Feb,Jan,Jul,Jun,Mar,May,Nov,Oct,Sep 
```

Enter fullscreen mode Exit fullscreen mode

…如果没有 compareFunction，数字就像字符串一样排序:

```
 console.log(['1', '5', '10', '15', '20'].sort());
  console.log([1, 5, 10, 15, 20].sort()); 
```

Enter fullscreen mode Exit fullscreen mode

```
 1,10,15,20,5
  1,10,15,20,5 
```

Enter fullscreen mode Exit fullscreen mode

如果您需要创建一个新的已排序的数组，而保持原来的数组未排序，您可以首先通过“slice”函数或 spread(“…”)操作符:
克隆该数组

```
 console.log(months);
  console.log(months.slice().sort());
  console.log([...months].sort());
  console.log(months); 
```

Enter fullscreen mode Exit fullscreen mode

```
 Jan,Feb,Mar,Apr,May,Jun,Jul,Aug,Sep,Oct,Nov,Dec
  Apr,Aug,Dec,Feb,Jan,Jul,Jun,Mar,May,Nov,Oct,Sep
  Apr,Aug,Dec,Feb,Jan,Jul,Jun,Mar,May,Nov,Oct,Sep
  Jan,Feb,Mar,Apr,May,Jun,Jul,Aug,Sep,Oct,Nov,Dec 
```

Enter fullscreen mode Exit fullscreen mode

下面是带有 compareFunction 回调的 sort 方法的例子:

```
 const sortedNumbers = [1, 5, 10, 15, 20].sort((num1, num2) => num1 - num2);
  console.log(sortedNumbers);

  const sortedByDaysInMonth = months.sort((m1, m2) => getDaysInMonth(m1) - getDaysInMonth(m2));

  console.log(sortedByDaysInMonth); 
```

Enter fullscreen mode Exit fullscreen mode

```
 1,5,10,15,20
  Feb,Jun,Sep,Apr,Nov,Jul,Dec,Jan,Mar,Oct,May,Aug 
```

Enter fullscreen mode Exit fullscreen mode

## [array.concat](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/concat)

*   目的:将两个数组合并成一个新数组
*   返回:新数组
*   LINQ 等价:[IEnumerable<T1T>。工会](https://docs.microsoft.com/en-us/dotnet/api/system.linq.enumerable.union?view=netframework-4.8)

```
const months1 = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun'];
const months2 = ['Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'];
const months = months1.concat(months2);
console.log(months); 
```

Enter fullscreen mode Exit fullscreen mode

```
Jan,Feb,Mar,Apr,May,Jun,Jul,Aug,Sep,Oct,Nov,Dec 
```

Enter fullscreen mode Exit fullscreen mode