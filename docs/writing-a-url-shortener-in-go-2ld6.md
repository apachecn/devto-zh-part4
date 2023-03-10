# 如何在 Go 中编写网址缩写

> 原文：<https://dev.to/nwneisen/writing-a-url-shortener-in-go-2ld6>

Gophercises 的练习 2 是创建一个 URL 缩短器。该代码的框架已经给出，学生需要填写缺失的部分。给出了主要问题的解决方案，但没有给出奖金挑战的解决方案。这是我对主要问题以及额外挑战的解决方案，其中没有给出解决方案。

这个练习的主要目标是熟悉在 Go 中使用不同的标记语言。它还介绍了使用命令行参数和从数据库中读取数据。

## 网址缩写

问题的第一步是创建一个地图处理程序。映射处理程序需要使用指定的 URL 检查映射中的键，并将用户重定向到存储在该键中的值。如果没有找到关键字，那么应该使用回退处理程序。

```
return http.HandlerFunc(func(rw http.ResponseWriter, r *http.Request) {
    if pathsToUrls[r.URL.Path] != "" {
        http.Redirect(rw, r, pathsToUrls[r.URL.Path], http.StatusFound)
    }

    fallback.ServeHTTP(rw, r)
}) 
```

Enter fullscreen mode Exit fullscreen mode

下一步是为 YAML 输入创建一个处理程序。该函数将解析 YAML 输入并根据结果构建地图。然后，该映射被传递给在上一步中构建的映射处理程序。

```
var redirects []struct {
    Path string `yaml:"path"`
    URL  string `yaml:"url"`
}

err := yaml.Unmarshal([]byte(yml), &redirects)
if err != nil {
    return nil, err
}

paths := make(map[string]string)
for _, path := range redirects {
    paths[path.Path] = path.URL
}

return MapHandler(paths, fallback), nil 
```

Enter fullscreen mode Exit fullscreen mode

## 奖励#1:从文件中读取 YAML

给定的代码在程序内部创建了一个示例 YAML 字符串。这个额外的挑战是从文件中读取 YAML。文件路径将通过命令行传递，并有一个默认值。

第一步是从命令行读取文件路径。这是使用 Golang 国旗包完成的。标志包使得设置命令行参数变得非常容易。

```
yamlPath := flag.String("YAML", "paths.yaml", "A YAML file")
flag.Parse() 
```

Enter fullscreen mode Exit fullscreen mode

这里库已经设置好了，所以它知道它将通过命令行接收一个字符串参数。该参数将被称为“yaml ”,默认值为“paths.yaml ”,描述为“YAML 文件”。该参数的值将存储在 yamlPath 变量中。旗子。然后调用 Parse()函数，该函数将处理命令行参数并将它们存储在关联的变量中。

使用标志库的一个好处是，有了这一行代码，程序现在会有一个自动生成的帮助菜单。将`--help`标志传入你的程序将为试图找出可用标志的人打印一个菜单。

```
Usage of /tmp/go-build244514782/b001/exe/main:
  -YAML string
        A YAML file containing redirects (default "paths.yaml") 
```

Enter fullscreen mode Exit fullscreen mode

现在需要将文件路径传递给 YAML 处理程序，打开它并从文件中读取。从文件中读取输入时，有几个不同的选项可用。

```
reader, err := os.Open(*yamlPath)
if err != nil {
    panic(err)
}

err = yaml.NewDecoder(reader).Decode(&redirects)
if err != nil {
    return nil, err
} 
```

Enter fullscreen mode Exit fullscreen mode

创建一个 io.reader 允许这个 reader 被传递给 YAML 库。这允许 YAML 库管理从文件读取的缓冲。然后，它被传递给一个新的 YAML 解码器，该解码器将文件解码成我们的重定向变量。

其他选项包括将整个文件读入内存，或者一次读入文件中的一行。将整个文件读入内存会增加程序的内存使用量，尤其是在有大量路径的情况下。一次读入文件中的一行可以防止这个问题。幸运的是，这类似于解码器缓冲文件的方式，无需手动管理文件的位置指针。

这就是处理存储在文件中的 YAML 输入的方法。

## 奖励#2:构建一个 JSON 处理程序

下一个奖励问题与主挑战非常相似，但略有不同。这一次，将使用 JSON 而不是原始的 YAML 传递数据。

```
reader, err := os.Open(*jsonPath)
if err != nil {
    panic(err)
}

err = json.NewDecoder(reader).Decode(&redirects)
if err != nil {
    return nil, err
} 
```

Enter fullscreen mode Exit fullscreen mode

除了使用 JSON 库而不是 YAML 库之外，这里没有太多变化。这些库被设置为使用相同的接口，因此在两者之间切换非常方便。

## 奖励 3:构建数据库处理程序

最后一个额外的挑战是将路径存储在数据库中，并从程序中访问它们。在练习中推荐使用 BoltDB，但是我选择使用 MongoDB。MongoDB 是我在生产系统中听说过的一个名字，我已经在我的系统上安装了它。安装和设置 MongoDb 的说明可以在[官方网站](https://docs.mongodb.com/manual/installation/)上找到。

```
var db *mongo.Client

collection := db.Database("test").Collection("Paths")
cur, err := collection.Find(context.TODO(), bson.M{})
if err != nil {
    log.Panic(err)
} 
```

Enter fullscreen mode Exit fullscreen mode

`db`被用作具有包范围的全局变量。假设`db`已经在另一个函数中初始化。代码首先访问“test”数据库上的“Paths”集合。然后，使用一个空接口对该集合进行搜索。空接口导致从集合中检索所有行。如果接口中有值，那么结果将被这些值过滤。`Context.TODO()`是一个空上下文，在上下文不可用时使用。

将另一个处理程序的数据转换成地图非常简单。当使用数据库时，这个过程稍微复杂一些。

```
paths := make(map[string]string)

for cur.Next(context.TODO()) {
    var redirect Redirect
    err := cur.Decode(&redirect)
    if err != nil {
        log.Panic(err)
    }

    paths[redirect.Path] = redirect.URL
} 
```

Enter fullscreen mode Exit fullscreen mode

`Collection.Find()`在前面的代码中将光标返回到集合中的行。这个游标现在可以用来迭代集合中的行，并将它们的值添加到映射中。每次迭代时都会创建一个要添加到映射中的结构。然后，路径被用作地图中的键，URL 作为值。然后，可以将地图传递给最初创建的 MapHandler。

## 包装完毕

这种 Gophercise 是习惯使用不同数据存储格式的好方法。额外的挑战有助于慢慢引入额外的库，这些库很好了解并且有一些使用经验。