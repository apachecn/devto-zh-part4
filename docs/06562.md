# JavaScript 依赖项的自定义类型定义

> 原文：<https://dev.to/ankursheel/custom-type-definitions-for-javascript-dependencies-5gj5>

如果您正在将 javascript 依赖项导入到 typescript 应用程序中，您可能会遇到以下错误

***"找不到模块“第三方库名”的声明文件。“third-party-library-name.js”具有“any”类型。请尝试 NPM install @ types/third-party-library-name(如果它存在),或者添加包含声明模块“third-party-library-name”的新声明(. d.ts)文件*T3】**

如果您阅读了错误消息，您可以看到它建议检查 DefinitelyTyped 存储库。

如果你幸运的话，你会发现它是一个资源库，并且可以使用 *npm* 或者 *yarn* 来安装它。

但是如果你没有那么幸运呢？

## 解决方案 1

现在，只有当你的 *tsconfig.json* 中有`noImplicitAny: true`时，你才会看到这个错误。所以你可以通过设置`noImplicitAny: false`来避免这个错误。

但是，这样做会失去类型安全，所以让我们排除这个可行的选择。

## 方案二

添加本地声明文件。

### 步骤

那么，你具体是怎么做的呢？

1.  创建一个 ***类型的*** 文件夹。
2.  编辑 tsconfig.json 以使用 typeRoots 属性，以便编译器可以找到本地声明文件。您将需要它包含您的本地文件夹以及 *node_modules/@types* 。您还需要检查本地文件夹，然后再检查*节点 _ 模块*文件夹。

```
"compilerOptions":  {  ...  "typeRoots":  ["./types",  "node_modules/@types"]  } 
```

1.  添加属性以排除 tsconfig 中的属性，这样它就不会被编译。

```
"exclude":  [...,  "types"] 
```

1.  在*类型*目录下添加一个 ***第三方库名*T3 文件夹。**
2.  在该文件夹中添加一个 *index.d.ts* 。
3.  添加您的类型定义。但是如果像我一样，您想要原型化东西或者不想编写完整的自定义类型，您可以通过将它添加到 *index.d.ts* 文件来声明模块。

```
declare module 'third-party-library-name'; 
```

唯一的警告是你没有任何智能感知。

## 结论

希望这篇文章能帮助你，你不必求助于从你的项目中移除 Typescript 或者通过在 tsconfig.json 中添加`noImplicitAny: false`来失去类型安全。