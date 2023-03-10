# 阴性测试

> 原文：<https://dev.to/rfornal/negative-testing-4k55>

> 作为干净利落地处理失败的一部分，一个组织必须做负面测试。

## 关于阴性检测

软件测试中有两种主要的测试策略:积极测试和消极测试。

**阳性测试**确定应用程序按预期工作。如果在阳性测试中遇到错误，则测试失败。

**负面测试**确保应用程序可以优雅地处理无效输入或意外的用户行为。例如，如果用户试图在数字字段中键入一个字母，在这种情况下，正确的行为可能是显示一个警告。消极测试的目的是检测这种情况，防止应用程序崩溃。此外，消极测试有助于提高应用程序的质量并找到它的弱点。

当您执行阴性测试时，异常是意料之中的...它们表明应用程序正确地处理了不适当的用户行为。

一个百分之百没有错误的系统是不可行的，但是如果团队拥有软件质量的所有权，那么团队可以确保他们已经做了一切来防止失败。

通常称为错误路径测试或失败测试的负面测试通常是为了确保代码的稳定性。

负面测试是 ***针对无效数据*** 验证应用的过程。负面测试的预期目的是确定坏数据是否得到妥善处理。

## 漏洞测试

在开发和修复 bug 时，有时会看到 bug 是因为没有足够的防护措施而导致的。当这些防护措施到位后，应该用适当的负面测试来增强测试，以确保 bug 不再出现。

## 阴性检测的优势

*   阴性测试对于确保产品质量非常重要。一个质量好的产品是零漏洞的产品，确保阴性测试是非常重要的。
*   编写阴性测试可以确保覆盖所有可能的情况。不管是有意还是无意，都有可能出现负面的测试用例。为了确保涵盖所有案例，我们必须在进行阳性测试的同时进行阴性测试。
*   客户会对产品更有信心。

## 阴性检测的弊端

*   很多情况下，不需要过多的阴性测试。在否定测试用例中确定条件是非常重要的。有时候我们不需要在一个特定的系统上做负面测试。
*   消极测试需要有经验的人来创建消极测试用例。

## 典型场景

支持这些场景的代码在 GitHub Repo 中:[这里](https://github.com/bob-fornal/negative-testing)

### 所需数据

必需数据意味着函数有一个必需的参数。要检查这种行为，请创建一个测试，将必填字段留空、null 或未定义，并分析响应。

```
function testable001(testField) {
  if (testField === null || testField === undefined) {
    return 'invalid';
  }
  return 'valid';
} 
```

Enter fullscreen mode Exit fullscreen mode

为了测试这段代码...

```
describe('testable001 [negative inputs]', function() {
  it('expects no parameter to short out correctly', function() {
    var result = testable001();
    expect(result).toEqual('invalid');
  });
  it('expects null to short out correctly', function() {
    var result = testable001(null);
    expect(result).toEqual('invalid');
  });
  it('expects undefined to short out correctly', function() {
    var result = testable001(undefined);
    expect(result).toEqual('invalid');
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 具体数据类型

这是函数需要特定类型的数据(数字、日期、文本等)的时候。).若要验证此代码是否正常运行，请创建一个测试，将不正确的数据传递给该函数。

```
function testable002(testField) {
  if (typeof testField !== 'number') {
    return -1;
  }
  return testField * 2;
} 
```

Enter fullscreen mode Exit fullscreen mode

为了测试这段代码...

```
describe('testable002 [negative inputs]', function() {
  it('expects string to short out correctly', function() {
    var result = testable002('not this text');
    expect(result).toEqual(-1);
  });
  it('expects null to short out correctly', function() {
    var result = testable002(null);
    expect(result).toEqual(-1);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 字符数

函数有时会限制可以传入的字符数。要检查应用程序的行为，请创建一个测试，向函数传递超过允许数量的字符。

```
function testable003(testField) {
  const maxLength = 10;
  return testField.substr(0, maxLength);
} 
```

Enter fullscreen mode Exit fullscreen mode

为了测试这段代码...

```
describe('testable003 [negative inputs]', function() {
  it('expects length to max out correctly', function() {
    var result = testable003('01234567890123456789');
    expect(result).toEqual('0123456789');
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 合理数据

这包括具有合理限制的函数参数，例如，不允许输入 200 或负数作为年龄值。要检查此行为，请创建一个在指定字段中输入无效数据的阴性测试。

```
function testable004(testField) {
  if (testField <= 0 || testField > 120) {
    return 'invalid age';
  }
  return 'valid age';
} 
```

Enter fullscreen mode Exit fullscreen mode

为了测试这段代码...

```
describe('testable004 [negative inputs]', function() {
  it('expects age to handle zero correctly', function() {
    var result = testable004(0);
    expect(result).toEqual('invalid age');
  });
  it('expects age to handle -1 correctly', function() {
    var result = testable004(-1);
    expect(result).toEqual('invalid age');
  });
  it('expects age to handle 200 correctly', function() {
    var result = testable004(200);
    expect(result).toEqual('invalid age');
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 会话测试

这里我们讨论的是一些外部需求，比如用户的登录状态，在函数返回正确的值之前需要检查这些需求。要正确检查该函数，请创建一个测试，将外部值设置为不正确的状态，并检查函数的结果。

此处设置代码...

```
var state = {
  loggedIn: true
}; 
```

Enter fullscreen mode Exit fullscreen mode

测试中的函数...

```
function testable005(state, testField) {
  if (state.loggedIn !== true) {
    return false;
  }
  return testField;
} 
```

Enter fullscreen mode Exit fullscreen mode

为了测试这段代码...

```
describe('testable005 [negative inputs]', function() {
  it('expects logged out to be handled correctly', function() {
    state.loggedIn = false;
    var result = testable005(state, 'test');
    expect(result).toEqual(false);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 分析阴性测试模式

查看代码示例可以发现，确定适当测试数量的方法可以来自几个不同的领域。为了确定覆盖哪些区域，我们可以使用边界值分析和等价划分。

### 边界值分析

顾名思义，边界表示对某事物的限制。因此，这涉及到设计只关注边界值和验证代码行为的测试场景。因此，如果提供的参数在边界值内，则认为是阳性测试，而超出边界值的输入被认为是阴性测试的一部分。

### 等价划分

在等价划分中，测试数据被划分到不同的分区中。这些分区被称为等价数据类。假设每个分区中的各种输入数据(数据可以是一个条件)行为相同。

因此** *每个分区只需要测试* *一种特定情况。如果其中一个正常工作，那么该分区中的所有其他分区都会正常工作。类似地，如果一个分区中的一个条件不起作用，那么其他条件都不起作用。

因此，很明显，有效数据类(在分区中)将由肯定测试组成，而无效数据类将由否定测试组成。

## 结论

当您执行阴性测试时，异常是意料之中的...它们表明应用程序正确地处理了不适当的用户行为。

一个百分之百没有错误的系统是不可行的，但是如果团队拥有软件质量的所有权，那么团队可以确保他们已经做了一切来防止失败。