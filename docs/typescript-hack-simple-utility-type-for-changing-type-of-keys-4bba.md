# Typescript hack:用于更改键类型的简单实用程序类型

> 原文：<https://dev.to/denniscual/typescript-hack-simple-utility-type-for-changing-type-of-keys-4bba>

当在 Typescript 中使所有键可选时，我们将使用实用程序类型 [Partial](https://netbasal.com/getting-to-know-the-partial-type-in-typescript-ecfcfbc87cb6) 。但是如果我们想改变
给定对象类型的一些键的类型呢？

我创建了一个简单的实用程序类型来实现这个目标。首先，我将向您展示如何使用它，最后展示代码。

```
type User = {
  id: number
  name: string
  age: number
  hobbies: []
}

type OtherUser = ChangeTypeOfKeys<User, 'id' | 'age', string> 
```

这是简单的实用程序。首先，它接受目标`object`类型。然后是`keys`，它是一个`union`类型，我们想改变并最终通过新类型。创建的`OtherUser`类型将具有如下形状:

```
type OtherUser = {
 id: string
 name: string
 age: string
 hobbies: []
} 
```

太棒了。我们只是把键的类型从`number`改成了`string`。本实用新型的代码:

```
/**
 * Change the type of Keys of T from NewType
 */
export type ChangeTypeOfKeys<
  T extends object,
  Keys extends keyof T,
  NewType
> = {
  // Loop to every key. We gonna check if the key
  // is assignable to Keys. If yes, change the type.
  // Else, retain the type.
  [key in keyof T]: key extends Keys ? NewType : T[key]
} 
```

就这些！如果你认为这有帮助。喜欢并分享！