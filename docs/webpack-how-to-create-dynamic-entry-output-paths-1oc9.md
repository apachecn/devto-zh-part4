# Webpack:如何创建动态入口和输出路径

> 原文：<https://dev.to/bbenefield89/webpack-how-to-create-dynamic-entry-output-paths-1oc9>

# TLDR；

对于那些不想阅读，只是想要配置文件，然后在这里你去。你所需要做的就是改变传递给`glob.sync(...)`方法的**路径字符串**来满足你的需要。

```
const glob = require('glob')
const path = require('path')

module.exports = {
    entry: glob.sync('./Projects/**/index.js').reduce((acc, path) => {
        const entry = path.replace('/index.js', '')
        acc[entry] = path
        return acc
    }, {}),

    output: {
        filename: './[name]/main.js',
        path: path.resolve(__dirname)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

用 Webpack 创建动态入口和输出路径的想法可能看起来令人困惑。当我试图弄清楚情况并开始四处询问时，我收到了很多“你为什么需要那个？”。

“为什么”很简单。我发现自己处于这样一种情况，我写了很多一次性的脚本，每一个都做自己的事情，通常与另一个脚本无关。因此，我对我的项目有一些非常特殊的需求。主要是，有能力建立和缩小每个脚本到他们自己的子目录。

这个 **Webpack** 配置文件的真正魔力在于`entry`属性。`entry`属性能够接受:

*   `string`

```
entry: './my/path/index.js' 
```

Enter fullscreen mode Exit fullscreen mode

*   `string[]`

```
entry: ['./my/first/path/index.js', './my/second/path/index.js'] 
```

Enter fullscreen mode Exit fullscreen mode

*   `object`

```
entry: {
    'my/first/path': './my/first/path/index.js',
    'my/second/path': './my/second/path/index.js'
} 
```

Enter fullscreen mode Exit fullscreen mode

*   `Function => string | string [] | object`

```
entry: () => './my/path/index.js' 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们深入了解这个配置文件中实际发生了什么。确保**阅读**注释，因为我将尝试以注释的形式解释代码中发生的一切。

**webpack.config.js**

```
/**
 * When passed a string, Glob will attempt to find each file that matches the
 * path given and return each path to the file as string[]
 */
const glob = require('glob')

/**
 * The Path API will be used to get the absolute path to the directory where we
 * plan to run Webpack
 */
const path = require('path')

module.exports = {
    /**
     * Pass Glob a relative path to each of our entry points
     * We will have different subdirectories inside of the Project directory so
     * we need to replace any of the directory names with a wildcard, **, which 
     * will recursively match any combination of directory names inside of any
     * number of subdirectories until it finds the index.js entry.
     * Then we use the Array.prototype.reduce method to iterate through the array
     * and return an object containing a path to each of our entry files
     * (index.js)
     */
    entry: glob.sync('./Projects/**/index.js').reduce((acc, path) => {
        /**
         * The "[name]" placeholder in the "output" property will be replaced
         * with each key name in our "entry" object. We need to make sure the
         * keys are a path to the "index.js" file but without the actual file
         * name. This is why we replace the file name, "index.js", with a string
         */
        const entry = path.replace('/index.js', '')
        /**
         * Here we start building our object by placing the "entry" variable from
         * the previous line as a key and the entire path including the file name
         * as the value
         */
        acc[entry] = path
        return acc
    }, {}),

    /**
     * The "output" property is what our build files will be named and where the
     * build file will be placed
     */
    output: {
        /**
         * Again, the "[name]" place holder will be replaced with each key in our
         * "entry" object and will name the build file "main.js"
         */
        filename: './[name]/main.js',
        /**         
         * We need to provide an absolute path to the root of our project and
         * thats exactly what this line is doing
         */
        path: path.resolve(__dirname)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 结果

这就是使用 **Webpack** 创建动态入口和输出路径所需的全部内容。现在的问题是这个例子使用什么样的项目架构？

```
> node_modules
  package.json
  package-lock.json
> Projects
  ---- > Proj_1
         ---- index.js
  ---- > Proj_2
         ---- index.js
  webpack.config.js 
```

Enter fullscreen mode Exit fullscreen mode

运行我们的 **Webpack** 并构建我们的文件后，我们的项目将看起来像

```
> node_modules
  package.json
  package-lock.json
> Projects
  ---- > Proj_1
         ---- index.js
         ---- main.js  // new build file
  ---- > Proj_2
         ---- index.js
         ---- main.js  // new build file
  webpack.config.js 
```

Enter fullscreen mode Exit fullscreen mode