# 酷派 ES2019 使用案例

> 原文：<https://dev.to/christianmemije/cool-es2019-use-cases-2og2>

ES2019 为 JavaScript 引入了许多有用的新功能。我将浏览一些很酷的用例。

### 有条件地向数组中添加项目

假设您想有条件地将一个项添加到数组中的给定索引处，比如索引 1。

#### es 2019 年之前

您可以使用 if 语句，并使用`splice()`在所需的索引处插入项目。

```
// @ts-check

const headlessMode: boolean = false;

let configArr = [
  '--disable-gpu',
  '--window-size=1274,1274'
];
const index: number = 1;
if (headlessMode) {
  configArr.splice(index, 0, '--headless');
}

console.log({ configArr });

/*
{
  configArr: [
    '--disable-gpu',
    '--window-size=1274,1274'
  ]
}
*/

export {}; 
```

Enter fullscreen mode Exit fullscreen mode

#### ES2019

如果条件不满足，可以在给定的索引处使用三元组并放置一个空数组。然后您可以调用`flat()`方法，该方法将展平数组，从而移除空数组。

```
// @ts-check

const headlessMode: boolean = false;

const configArr = [
  '--disable-gpu',
  headlessMode ? '--headless' : [],
  '--window-size=1274,1274'
].flat();

console.log({ configArr });

/*
{
  configArr: [
    '--disable-gpu',
    '--window-size=1274,1274'
  ]
}
*/

export {}; 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 展平嵌套数组

假设您想要展平未知深度的嵌套数组。

#### es 2019 年之前

你可以通过归约函数和递归来实现。

```
// @ts-check

const nestedArray: any = [[[1, [1.1, [1.11]]], 2, 3], [4, 5]];

function flatten(arr: number[]) {
  return arr.reduce(
    (flat: number[], toFlatten: any) =>
      flat.concat(
        Array.isArray(toFlatten) ?
        flatten(toFlatten) :
        toFlatten
      ),
    []
  );
}

const flatArray: number[] = flatten(nestedArray);

console.log({ flatArray });

// { flatArray: [ 1, 1.1, 1.11, 2, 3, 4, 5 ] }

export {}; 
```

Enter fullscreen mode Exit fullscreen mode

#### ES2019

可以调用参数设置为`Infinity`的`flat()`方法。

```
// @ts-check

const nestedArray: any = [[[1, [1.1, [1.11]]], 2, 3], [4, 5]];

const flatArray: number[] = nestedArray.flat(Infinity);

console.log({ flatArray });

// { flatArray: [ 1, 1.1, 1.11, 2, 3, 4, 5 ] }

export {}; 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 从元组创建对象

假设您想从元组数组中创建一个对象。

#### es 2019 年之前

您可以使用归约函数来实现这一点。

```
// @ts-check

type strOrNumArray = (string | number)[];
interface IObj {
  [s: string]: string;
}

const arrayPairs: strOrNumArray[] = [['foo', 1], ['bar', 2]];

const obj: IObj = arrayPairs.reduce(
  (o: IObj, item: strOrNumArray) => ({ ...o, [item[0]]: item[1] }), {}
);

console.log({ obj });

// { obj: { foo: 1, bar: 2 } }

export {}; 
```

Enter fullscreen mode Exit fullscreen mode

#### ES2019

可以用`Object.fromEntries()`。

```
// @ts-check

type strOrNumArray = (string | number)[];
interface IObj {
  [s: string]: string;
}

const arrayPairs: strOrNumArray[] = [['foo', 1], ['bar', 2]];

const obj: IObj = Object.fromEntries(arrayPairs);

console.log({ obj });

// { obj: { foo: 1, bar: 2 } }

export {}; 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 修剪前导或尾随空白

假设您只想修剪前导或尾随空白，而不是两者都修剪。

#### 2019 年前

您可以使用简单的正则表达式来做到这一点。

```
// @ts-check

const str: string = ' Whitespace ';

const leadingTrimmedStr = str.replace(/^\s*/, '');
const trailingTrimmedStr = str.replace(/\s*$/, '');

console.log({ leadingTrimmedStr, trailingTrimmedStr });

/*
{
  leadingTrimmedStr: '     Whitespace',
  trailingTrimmedStr: 'Whitespace     '
}
*/

export {}; 
```

Enter fullscreen mode Exit fullscreen mode

#### ES2019

您可以使用`trimStart()`或`trimEnd()`方法。

```
// @ts-check

const str: string = ' Whitespace ';

const leadingTrimmedStr = str.trimStart();
const trailingTrimmedStr = str.trimEnd();

console.log({ leadingTrimmedStr, trailingTrimmedStr });

/*
{
  leadingTrimmedStr: '     Whitespace',
  trailingTrimmedStr: 'Whitespace     '
}
*/

export {}; 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 加成:ES2020？(第三阶段提案)

### 安全访问深层对象密钥

假设您想以安全的方式访问一个对象内部的密钥。

#### es 2020 年之前

您可以使用`&&`操作符进行短路。

```
// @ts-check

const user: any = undefined;

var street = user && user.address && user.address.street;

console.log({ street });

// { street: undefined }

export {}; 
```

Enter fullscreen mode Exit fullscreen mode

#### ES2020

您可以使用`?`操作符。

```
// @ts-check

const user: any = undefined;

var street = user?.address?.street;

console.log({ street });

// { street: undefined }

export {}; 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 为未定义的对象键设置回退

假设您希望在某个键不在对象中时有一个后备。注意，您不希望使用`||`操作符，因为一个键可能在对象中，但值可能是 falsy。

#### es 2020 年之前

您可以使用`in`检查对象中是否有一个键，并设置一个回退。

```
// @ts-check

interface IConfig {
  headerText: string;
  animationDuration: number;
  showSplashScreen: boolean;
}
const config: IConfig = {
  headerText: '',
  animationDuration: 0,
  showSplashScreen: false,
};

const headerText: string = 'headerText' in config ?
  config.headerText :
  'header fallback';
const animationDuration: number = 'animationDuration' in config ?
  config.animationDuration :
  300;
const showSplashScreen: boolean = 'showSplashScreen' in config ?
  config.showSplashScreen :
  true;

console.log({
  headerText,
  animationDuration,
  showSplashScreen,
});

/*
{
  headerText: '',
  animationDuration: 0,
  showSplashScreen: false
}
*/

export {}; 
```

Enter fullscreen mode Exit fullscreen mode

#### ES2020

您可以使用`??`操作符来设置回退。

```
// @ts-check

interface IConfig {
  headerText: string;
  animationDuration: number;
  showSplashScreen: boolean;
}
const config: IConfig = {
  headerText: '',
  animationDuration: 0,
  showSplashScreen: false,
};

const headerText: string =
  config.headerText ??
  'header fallback';
const animationDuration: number =
  config.animationDuration ??
  300;
const showSplashScreen: boolean =
  config.showSplashScreen ??
  true;

console.log({
  headerText,
  animationDuration,
  showSplashScreen,
});

/*
{
  headerText: '',
  animationDuration: 0,
  showSplashScreen: false
}
*/

export {}; 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 有用的资源

*   [https://exploringjs.com/es2018-es2019/toc.html](https://exploringjs.com/es2018-es2019/toc.html)
*   [https://github.com/tc39/proposals](https://github.com/tc39/proposals)