# 如何在 JavaScript 中不对数组排序

> 原文：<https://dev.to/philnash/how-not-to-sort-an-array-in-javascript-l9o>

数组排序是你不会花太多时间考虑的事情之一，直到它不再为你工作。最近我在处理 JavaScript 中的一系列项目，这些项目根本没有正确排序，完全搞乱了界面。我花了很长时间才弄明白哪里出了问题，所以我想分享一下发生了什么，以及为什么会这么奇怪。

## 基本排序

JavaScript 在数组对象上有一个可用的`sort`方法，运行它可能会达到预期的效果。比如:

```
const stringArray = ['cat', 'dog', 'ant', 'butterfly'];
stringArray.sort();
// => ['ant', 'butterfly', 'cat', 'dog'] 
```

Enter fullscreen mode Exit fullscreen mode

如果你对可能有成员是`undefined`的数组进行排序的话，这就更好了。 [MDN 说“所有未定义的元素都被排序到数组的末尾。”](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)。

```
const stringArrayWithUndefined = [
  'cat',
  undefined,
  'dog',
  undefined,
  'ant',
  'butterfly',
  'zebra'
];
stringArrayWithUndefined.sort();
// => ['ant', 'butterfly', 'cat', 'dog', 'zebra', undefined, undefined] 
```

Enter fullscreen mode Exit fullscreen mode

## 明白了

您可能遇到的第一个问题是，如果您发现自己有一个包含`null`的数组。

```
const stringArrayWithUndefinedAndNull = [
  'cat',
  undefined,
  'dog',
  undefined,
  'ant',
  null,
  'butterfly',
  'zebra'
];
stringArrayWithUndefinedAndNull.sort();
// => ['ant', 'butterfly', 'cat', 'dog', null, 'zebra', undefined, undefined] 
```

Enter fullscreen mode Exit fullscreen mode

排序将把`null`强制转换成字符串`"null"`，它将出现在字母表的中间。

然后是数字。默认的 JavaScript 排序算法是将数组的所有成员转换为字符串，然后比较它们的 UTF-16 代码单元值序列。正如我们已经看到的，这对于字符串数组非常有效，但是对于数字它很快就失效了。

```
const numberArray = [5, 3, 7, 1];
numberArray.sort();
// => [1, 3, 5, 7]

const biggerNumberArray = [5, 3, 10, 7, 1];
biggerNumberArray.sort();
// => [1, 10, 3, 5, 7] 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，10 排在 3 之前，因为“10”排在“3”之前。

我们可以通过为 JavaScript 提供一个用于执行排序的比较函数来解决这个问题。该函数从数组中接收两个项目，它需要返回一个数值，该值是大于、小于还是等于零定义了项目相对于彼此的排序方式。如果返回值小于零，则第一项排序在第二项之前，如果值大于零，则第二项排序在第一项之前。如果返回值为 0，则这些项相对于彼此保持相同的顺序。

要对数字进行升序排序，比较函数比较简单:

```
const compareNumbers = (a, b) => a - b; 
```

Enter fullscreen mode Exit fullscreen mode

从第二项中减去第一项满足上述要求。将这个比较函数与前面的`biggerNumberArray`一起使用，可以正确地对数字进行排序。

```
biggerNumberArray.sort(compareNumbers);
// => [1, 3, 5, 7, 10] 
```

Enter fullscreen mode Exit fullscreen mode

如果您有`undefined`元素，这仍然有效，因为它们被忽略并被排序到末尾。

```
const numberArrayWithUndefined = [5, undefined, 3, 10, 7, 1];
numberArrayWithUndefined.sort(compareNumbers);
// => [1, 3, 5, 7, 10, undefined] 
```

Enter fullscreen mode Exit fullscreen mode

尽管如此，还是会引起问题。

```
const numberArrayWithUndefinedAndNull = [5, undefined, 3, null, 10, 7, 1];
numberArrayWithUndefinedAndNull.sort(compareNumbers);
// => [null, 1, 3, 5, 7, 10, undefined] 
```

Enter fullscreen mode Exit fullscreen mode

发生这种情况是因为将`null`强制为一个数字会返回 0。

```
Number(null);
// => 0 
```

Enter fullscreen mode Exit fullscreen mode

您可以在自己的`compareNumbers`函数中处理这个问题，或者对它的一致性感到高兴。

## 不一致的问题

最大的问题，也是我最近发现的，是当`undefined`以另一种方式潜入时。正如我们已经看到的，如果数组包含`undefined`，它将被忽略，只排序到后面。然而，如果你正在排序的对象的关键字可能是`undefined`，这种自动排序不会发生，结果会变得不一致。

例如，如果您有一个对象数组，其中有些对象有值，有些没有值，那么尝试按该值排序不会得到您想要的结果。

```
const objectArray = [
  { value: 1 },
  { value: 10 },
  {},
  { value: 5 },
  { value: 7 },
  { value: 3 }
];
const compareObjects = (a, b) => a.value - b.value;
objectArray.sort(compareObjects);
// => [ { value: 1 },
// { value: 10 },
// {},
// { value: 3 },
// { value: 5 },
// { value: 7 } ] 
```

Enter fullscreen mode Exit fullscreen mode

从`undefined`中减去一个数或者从一个数中减去`undefined`都返回`NaN`，因为这不在`sort`从比较函数中需要的数的范围内，结果最终有点奇怪。在这种情况下，导致问题的项停留在数组中它开始的地方，其他对象在本地排序。

有一些方法可以解决这个问题，但重要的是知道它会发生。在我的案例中，当我遇到这种情况时，我过滤掉没有价值的项目，因为它们在有价值之前并不重要。

```
objectArray.filter(obj => typeof obj.value !== 'undefined').sort(compareObjects);
// => [ { value: 1 },
// { value: 3 },
// { value: 5 },
// { value: 7 },
// { value: 10 } ] 
```

Enter fullscreen mode Exit fullscreen mode

## 当心排序

所有这些的结果是`sort`函数并不像看起来那么简单。字符串工作，数字需要一些输入，当`undefined`被作为原语处理时，你必须注意强制`null`或`undefined`对象值。

你在 JavaScript 或其他语言中遇到过排序问题吗？我也很想听听你们的故事，所以请在 Twitter @ philnash 上给我留言。