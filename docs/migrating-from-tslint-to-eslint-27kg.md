# 从 TSLint 迁移到 ESLint

> 原文：<https://dev.to/lcoenen/migrating-from-tslint-to-eslint-27kg>

我知道我们的代码库有一些污垢，而我自己在重构时已经忘记了许多未使用的导入。我惊恐地意识到我们没有在棉绒中使用`no-unused-import`-[t 棉绒](https://palantir.github.io/tslint/)。

我试图激活它，但结果是这条规则已经被[否决了](https://github.com/palantir/tslint/issues/4046)，被否决了又被否决了。事实上，整个工具[将被放弃](%EF%BB%BF%EF%BB%BFhttps://medium.com/palantir/tslint-in-2019-1a144c2317a9)，每个人都被邀请迁移到 [ESLint](https://eslint.org/) ，他们的目标是与他们合并，模仿尽可能接近 Javascript 及其生态系统的 Typescript 取向。

到目前为止，一切顺利——不同工具的倍增(甚至可以说是入侵)对任何 javascript 开发人员来说都是一种痛苦。无论你的栈是什么，你可能已经尝试将两个工具集成在一起，只是意识到它与你已经使用的另一个零件库冲突。Javascript 的流动、“微观”方面有时是一种乐趣，但也可能是一种真正的痛苦。

所以，让我们使用`npm install --save-dev eslint typescript-eslint/eslint-plugin`来安装 ESlint 和 typescript 插件

我们用的是 ES6 和更漂亮的版本，所以下面是我们的`.eslintrc`的样子:

```
{
  parser: '@typescript-eslint/parser',
  parserOptions: {
    ecmaVersion: 2018,
    sourceType: 'module',
  },
  plugins: ['@typescript-eslint'],
  env: {
    browser: true,
    node: true,
        es6: true
  },
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
        'prettier',
        'prettier/@typescript-eslint'
  ],
    rules:  {
        '@typescript-eslint/explicit-function-return-type': 0
    }
} 
```

就是这样。现在，你只需要修复 ESLint 刚刚向你吐槽的 808 个错误。

===另请参见===

[https://github . com/typescript-eslint/typescript-eslint/tree/master/packages/eslint-plugin](https://github.com/typescript-eslint/typescript-eslint/tree/master/packages/eslint-plugin)