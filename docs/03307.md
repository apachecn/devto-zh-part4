# 用 Jest 进行数据驱动的单元测试

> 原文：<https://dev.to/flyingdot/data-driven-unit-tests-with-jest-26bh>

> 马库斯·斯皮斯克在 [Unsplash](https://unsplash.com/search/photos/developer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

数据驱动的单元测试是用一系列输入数据运行测试的一种简单而有效的方法。这在以下情况下很有用

*   根据更广泛的价值观进行测试
*   测试空值和未定义的值
*   针对边缘情况进行测试，例如日期(月初、月末等)
*   或者调整您的单元测试统计——因为每个输入行都被算作一个单独的测试。你的经理会印象深刻的。😉

## 简单样品

让我们定义一个简单的测试来检查一个数字是否是偶数:

```
it('should be even', () => {
    expect(2 % 2).toBe(0);
}); 
```

```
 ✓ is even 
```

为了检查一系列数字，我们可以使用 Jest 的`it.each`(或者`test.each`，因为`it`只是`test`的别名)。我们将输入数据作为一个数组传递，然后逐个测试每一项(`n`)。

```
it.each([2,4,6])('should be even', (n) => {
    expect(n % 2).toBe(0);
}); 
```

```
✓ should be even
✓ should be even
✓ should be even 
```

我们可以使用[几个预定义的](https://jestjs.io/docs/en/api#testeachtable-name-fn-timeout)标记来改进测试的名称，这使得定位失败的测试更加容易:

```
it.each([2,4,5,6])('%i should be even', (n) => {
    expect(n % 2).toBe(0);
}); 
```

```
✓ 2 should be even
✓ 4 should be even
✕ 5 should be even
✓ 6 should be even 
```

## 多个参数

您也可以向测试传递多个参数。在下一个示例中，我们也将期望值传递给测试:

```
it.each([[2,4], [6,36], [8,64]])('can square %i to %s', (n, expected) => {
    expect(n*n).toBe(expected);
}); 
```

```
✓ can square 2 to 4
✓ can square 6 to 36
✓ can square 8 to 64 
```

输入表被定义为数组的数组。每个内部数组都是输入表中的一行。

我喜欢测试具有良好的可读性、可理解性和清晰的意图。重点应该放在实际的测试上，而不是实现上。Jest 提供了一个语法来支持这一点:

```
it.each`
      n     | expected ${1} | ${1}  ${2} | ${4}  ${4} | ${16}  ${12} | ${144} `('can square $n to $expected', ({ n, expected }) => {
            expect(n*n).toBe(expected);
}); 
```

```
✓ can square 1 to 1
✓ can square 2 to 4
✓ can square 4 to 16
✓ can square 12 to 144 
```

输入表现在看起来像一个实际的表。它被定义为一个模板字符串，其中第一行包含由竖线`|`分隔的变量名称。后续的每一行都是运行测试的输入数据的组合，定义为模板文字表达式。
你可以用`$variable`引用测试名里面的变量名。

## 运行多个测试

`.each`也可用于`describe`块，这使得针对一组输入数据运行多个测试甚至整个测试套件变得容易。

```
describe.each`
    x     | y ${10} | ${2}  ${9} | ${3}  ${8} | ${4} `('With x=$x and y=$y', ({x, y}) => {
    it('x should be larger then y', () => {
        expect(x).toBeGreaterThan(y);
    });

    it('should x should by dividable by y without rest', () => {
        expect(x % y).toBe(0);
    });
}); 
```

```
With x=10 and y=2
    ✓ x should be larger then y
    ✓ should x should by dividable by y without rest
With x=9 and y=3
    ✓ x should be larger then y (1ms)
    ✓ should x should by dividable by y without rest
With x=8 and y=4
    ✓ x should be larger then y
    ✓ should x should by dividable by y without rest (1ms) 
```

你甚至可以把`describe.each`和`it.each`结合起来，但是要注意可理解性。