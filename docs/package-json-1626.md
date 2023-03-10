# 我想读取 package.json 的各种元素！

> 原文：<https://dev.to/origamium/package-json-1626>

拉面是今天吃的。 往往存在想要写在 package.json 上的数据的情况。 例如`version`。 如果平时意识到要使用`npm version`命令，无论如何都会在 APP 程序内读取 package.json 内的`version`并显示出来，或者在重复`console.error`时，同时记录`version`并进行 Sentry 等错误监视

## Node.js

调用`process.env.npm_package_version`即可。 简单

## Web 应用

总之，虽说是 Web Application，但总觉得世界太大了，解说不完，所以提出两种方法。

*   读入`package.json`

如果是 ES6 的话，可以读取如下。

```
import packageJson from "../package.json"
console.log(packageJson.version) 
```

但是，`src`如果只允许读取以下目录，则无法直接读取 package.json。 作为处理方法，贴上 symbolic link 就可以了。

```
ln -s package.json src/package.alias.json 
```

这样可以读取如下。

```
import packageJson from "./package.alias.json"
console.log(packageJson.version) 
```

*   create-react-appの場合

读 package.json 本来就不平静(感觉)。 create-react-app 从一开始就各种各样，只要在`.env`中记载如下就可以了。

```
REACT_APP_VERSION=$npm_package_version 
```

于是，可以作为`process.env.REACT_APP_VERSION`读取。

**哎呀，这个在 Windows 上是不能动的哦！**
在 windows 上构建该代码时，`process.env.REACT_APP_VERSION`不显示版本信息，显示为`$npm_package_version`。 真为难啊。

在 Windows 上读取 npm 环境变量的格式如下:

```
REACT_APP_VERSION=%npm_package_version% 
```

但是这样的话无法在 macOS、Linux 上顺利读取。

解决方法是放弃上述. env 的做法，读取 package.alias.json，或者用泥臭的方法努力。

```
REACT_APP_VERSION_UNIX=$npm_package_version
REACT_APP_VERSION_WIN=%npm_package_version% 
```

```
export const appVersion = process.env.REACT_APP_VERSION_WIN === "%npm_package_version%" 
    ? process.env.REACT_APP_VERSION_UNIX 
    : process.env.REACT_APP_VERSION_WIN 
```

虽然很土，但是这样 Windows、macOS、Linux 都可以读取`version`信息。 我觉得如果要做这样的事的话，还是输入 package.json 比较好。

# 你还想要其他 npm 的环境变量吗？

`process.env`包含了各种各样的信息。 包括包名称的`npm_package_name`、包括许可信息的`npm_package_license`等。 请各自看。 我不想全部介绍。 尽管如此，我认为对于 Web 开发，只要能读懂 package.json 的内容就足够了。