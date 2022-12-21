# 用 Mocha 和 Chai 测试 TypeScript

> 原文：<https://dev.to/daniel_werner/testing-typescript-with-mocha-and-chai-5cl8>

## 简介

我是 TDD 的超级粉丝，已经写了一些关于这个主题的文章。在我最近的一个项目中，我使用 Angular 作为前端框架。通常我用 Jasmine 在 JavaScript 中进行测试(你可以在这里找到相关文章[)。Jasmine 应该也可以使用 TypeScript，但是那时，我在设置它时遇到了一些问题，所以我寻找另一个测试框架来使用。](https://42coders.com/unit-testing-javascript-with-jasmine/)

我发现 Mocha 与 Chai 断言库一起使用是一个很好的解决方案。让我们看看如何为 TypeScript 设置测试环境。

## 安装

第一步，我们用 npm 安装所需的包: **mocha** 、 **chai** 、 **ts-node** ，以及两个库的类型定义:

```
npm install chai mocha ts-node @types/chai @types/mocha --save-dev 
```

## 创建第一个测试

创建一个简单的测试函数:

```
export const helloTest(){ return true; } 
```

让我们创建我们的第一个测试用例，并断言 out 函数按预期工作。显然，在真正的 TDD 中，我们应该先写测试，再写函数，但出于演示的目的，这应该没问题。

```
import { helloTest } from '../src/hello-test';
import { expect } from 'chai';
import 'mocha';

describe('First test', 
  () => { 
    it('should return true', () => { 
      const result = helloTest();
      expect(result).to.equal(true); 
  }); 
}); 
```

## 运行测试

为了运行测试，我们将在 **package.json** 中添加一个脚本，注册 ts-node 来运行 mocha，并设置可以找到测试的路径，在本例中是在 tests 目录下:

```
"scripts": { "test": "mocha -r ts-node/register tests/**/*.spec.ts" }, 
```

我们现在可以用 npm 运行测试:

```
npm run test 
```

如果一切顺利，测试应该运行，您应该在控制台输出中看到它通过了。

## 结论

选择哪一个测试库是个人喜好的问题，最重要的是要有尽可能多的测试，它们帮助我们创建软件开发中所期望的可维护的和稳定的应用程序。

### 在[推特](https://twitter.com/vernerd)上关注我，了解更多软件开发技巧。

与摩卡和柴测试打字稿的帖子[最早出现在](https://42coders.com/testing-typescript-with-mocha-and-chai/) [42 码](https://42coders.com)上。