# Typescript + ESLint 和 StandardJS

> 原文：<https://dev.to/itmayziii/typescript-eslint-and-standardjs-5hmd>

2019 年 12 月 9 日更新——我已经创建了一个 ESLint 插件，它做的事情和本文一样，但是应该会让你的 ESLint 配置超级简单。
包可以在这里找到[。只需按照自述文件的说明进行操作，您就可以使用本文中描述的配置进行设置。](https://www.npmjs.com/package/eslint-plugin-standard-typescript)

既然 [Typescript 林挺正在将](https://eslint.org/blog/2019/01/future-typescript-eslint)从 [TSLint](https://palantir.github.io/tslint/) 转移到 [ESLint](https://eslint.org/) 是时候调整我们的配置了。如果你像我一样，你可能喜欢在编写 Typescript/Javascript 时使用[standards](https://standardjs.com/)作为你的官方风格。

当我使用 TSLint 时，我使用了一个名为 [tslint-config-standard](https://www.npmjs.com/package/tslint-config-standard) 的包，它使使用 StandardJS 变得简单。不幸的是，用 ESLint 设置 Typescript 要复杂一些，所以我想分享一下我的配置，希望能节省你几个小时的摸索时间。

`npm install --save-dev @typescript-eslint/eslint-plugin @typescript-eslint/parser eslint eslint-config-standard eslint-plugin-import eslint-plugin-node eslint-plugin-promise eslint-plugin-standard`

这有很多依赖项，所以我将逐一解释。

*   @ typescript-eslint/ESLint-plugin-特定于 ESLint 的插件，当与@typescript-eslint/parser 结合使用时，允许运行特定于 TypeScript 的林挺规则。
*   @ typescript-eslint/parser——一个特定于 ESLint 的解析器，它利用 typescript-estree，旨在替代 ESLint 的默认解析器 espree。
*   eslint——运行林挺规则的实际工具。
*   eslint-config-standard-standard js 规则的配置。
    *   eslint-插件-导入
    *   eslint-插件-节点
    *   eslint-插件-承诺
    *   eslint-plugin-standard 所有 eslint-config-standard 下的 eslint-plugin 都是那个包所需要的，我不确定为什么这个包没有把它们作为依赖项包含进来...😅

`.eslintrc.js`

```
module.exports = {
  'parser': '@typescript-eslint/parser',
  'parserOptions': {
    'project': './tsconfig.json', // Required to have rules that rely on Types.
    'tsconfigRootDir': './'
  },
  'extends': [
    'plugin:@typescript-eslint/recommended', // Out of the box Typescript rules
    'standard' // Out of the box StandardJS rules
  ],
  'plugins': [
    '@typescript-eslint' // Let's us override rules below.
  ],
  'rules': {
    '@typescript-eslint/no-use-before-define': 'off', // Allows us to hoist variables and functions which I am a fan of, functions not variables that is.
    '@typescript-eslint/no-explicit-any': 'off', // Too strict for my case, sometimes I need an any type
    '@typescript-eslint/member-delimiter-style': ['error', { // Prevents us from using any delimiter for interface properties.
      'multiline': {
        'delimiter': 'none',
        'requireLast': false
      },
      'singleline': {
        'delimiter': 'comma',
        'requireLast': false
      }
    }],
    '@typescript-eslint/indent': 'off', // This is the job of StandardJS, they are competing rules so we turn off the Typescript one. 
    'no-unused-vars': 'off', // On the fence about using this one, sometimes we import a package that is never used directly. 
    'node/no-unsupported-features/es-syntax': 'off' // Allows us to use Import and Export keywords.
  }
} 
```

出于好奇，这里是我的`tsconfig.json`

```
{  "compilerOptions":  {  "alwaysStrict":  true,  //  TODO  Needed  because  of  Apollo  https://github.com/apollographql/apollo-server/issues/1182,  this  should  be  considered  a  bug  with  Apollo  "allowSyntheticDefaultImports":  true,  "skipLibCheck":  true,  "outDir":  "dist",  "module":  "commonjs",  "declaration":  true,  "sourceMap":  true,  "target":  "es6",  "rootDir":  "./src",  "lib":  [  "es5",  "es6",  "es7",  "esnext.asynciterable"  ],  "experimentalDecorators":  true,  "emitDecoratorMetadata":  true,  "strict":  true  },  "include":  [  "src/**/*.ts"  ],  "exclude":  [  "node_modules",  "**/*.spec.ts",  "dist"  ]  } 
```

希望这个配置能帮到你或者节省你一些时间，非常感谢你在 twitter 上的关注