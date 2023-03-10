# 性能:与环境共舞

> 原文：<https://dev.to/negue/performance-tree-shake-with-environment-ts-ck3>

您可能会遇到这样的情况，您只需要在开发过程中存在的模块，而不需要在产品构建中存在。

例如:

在开发过程中，我几乎一直在使用`@ngrx/store-devtools`。

为了在我的产品构建中没有这个模块，我想，解决方案是简单的，只要使用环境条件将它从构建中排除。

<figure>

```
import {environment} from '../environments/environment';
import {StoreDevtoolsModule} from '@ngrx/store-devtools';

const additionalImports = [];

if (!environment.production) {
  additionalImports.push(StoreDevtoolsModule.instrument({
    maxAge: 25
  }));
}

@NgModule({
  import: [
    ...additionalImports
  ]
}) 
```

<figcaption>app.module.ts</figcaption>

</figure>

但是编译器仍然会将模块添加到产品版本中:大约为这个模块添加了`18 kb`，当然不是“那么多”,但是这加起来就是加载+解析/评估时间，这可能是你不需要的。

## 使用`environment.ts`更好的方式:)

由于 angular 为`environment.ts`和`environment.prod.ts`添加了一个默认的`fileReplacements`配置，这些配置将只在开发/生产构建期间编译。

我们也可以用它来为我们的开发阶段添加不同的模块，或者为生产阶段添加不同的模块。

<figure>

```
import {StoreDevtoolsModule} from '@ngrx/store-devtools';

export const environment = {
  production: false,
  modules: [
    StoreDevtoolsModule.instrument({
      maxAge: 25
    }),
  ]
}; 
```

<figcaption>environment.ts</figcaption>

</figure>

<figure>

```
import {environment} from '../environments/environment';

@NgModule({
  import: [
    ...environment.modules
  ]
}) 
```

<figcaption>app.module.ts</figcaption>

</figure>

## 🎉现在，该模块仅在开发过程中添加，而不是在您的生产构建中添加🎉

也许在未来有可能仅仅通过使用`if (environment.production)`来动摇这些种类，但是在那之前我们有一个很好的解决方法。

### `Protip 1:`为你的环境文件使用一个接口。

有一个类似于
的`environment.def.ts`文件

```
export interface Environment {
  production: boolean;
  modules: any[];
} 
```

这样两者(或者更多？)环境至少在属性上是一致的。

### `Protip 2:`使用路径别名

<figure>

```
 "compilerOptions":  {  "paths":  {  "@myApp/env":  ["apps/myApp/src/environments/environment"]  }  } 
```

<figcaption>tsconfig.json</figcaption>

</figure>

然后你可以通过`@myApp/env`而不是`../../../..and/so/on../../environment`将你的环境导入到任何文件中

* * *

### 我真的很想知道，你们是否只在开发/生产期间使用模块？