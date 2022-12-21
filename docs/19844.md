# 一个简单的二次解算器

> 原文：<https://dev.to/sudipto/a-nofrills-quadratic-solver-47fj>

上周，我用编程语言编写了我的第一个库，很高兴它是 JavaScript:)

链接到我的项目:[github.com/sudiptog81/quadratic-solver](https://github.com/sudiptog81/quadratic-solver)

## 示例用法

### 浏览器导入

```
<script src="https://unpkg.com/quadratic-solver"></script>
<script>
    console.log(qs.getVersion());
    const rootsArr = qs.quadSolver(2, 5, -3);
    rootsArr.map(root => console.log(`Root: ${root}`));
</script> 
```

### NodeJS 模块

```
const { quadSolver, getVersion } = require("quadratic-solver");
console.log(getVersion());
const rootsArr = quadSolver(2, 5, -3);
rootsArr.map(root => console.log(`Root: ${root}`)); 
```

## 方法

### quadSolver(a，b，c)

#### 论据

*   **a** :平方项的系数。
*   **b** :线性项的系数。
*   **c** :带符号的常数。

#### 返回值

*   **当两个根都是实数时** : `Array[2]`用方程的根。
*   **当只有一个根是真实的** : `Array[2]`其中第一个元素是真实的根，另一个元素是警告。
*   **当两个词根都是虚数时** : `Array[2]`其中两个元素都是警告。

**注意**:如果`a`等于`0`，`quadSolverCitardauq()`会按照提供的参数自动调用。

### 完成正方形(a，b，c)

#### 论据

*   **a** :平方项的系数。
*   **b** :线性项的系数。
*   **c** :带符号的常数。

#### 返回值

*   **当两个根都是实数时** : `Array[2]`用方程的解。
*   **当两个根都是虚数时** : `Array[2]`两个元素都在其中`NaN`。

### quadSolverCitardauq(a，b，c)

#### 论据

与 quadSolver(a，b，c)相同。

#### 返回值

与 quadSolver(a，b，c)相同。

### getVersion()

#### 论据

不需要任何参数。

#### 返回值

以`quadratic-solver v0.x.x`格式包含包的名称和版本的字符串。

## ES6 类

### 求解器(a，b，c)

#### 属性

*   **a** :平方项的系数；默认值:2。
*   **b** :线性项的系数；默认值:5。
*   **c** :带符号的常数；默认值:-3。

#### 方法

*   **solve()** :返回`quadSolver()`的输出以及实例的属性。
*   **completeSquare()** :返回`completeSquare()`的输出以及实例的属性。
*   **versionMethod()** :返回`getVersion()`的输出。

### MatrixSolver([[a，b，c]，[d，e，f]，...])

#### 属性

*   **matrix** :数组的数组，包含二次项和线性项的系数，后跟常数(按此顺序)；默认:`[[2, 5, -3]]`。

#### 方法

*   **solveAll()** :返回数组数组中每个数组的`quadSolver()`输出。
*   **completeSquareAll()** :返回数组数组中每个数组的`completeSquare()`输出。
*   **versionMethod()** :返回`getVersion()`的输出。