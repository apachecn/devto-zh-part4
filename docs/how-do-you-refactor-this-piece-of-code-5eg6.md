# 更新——如何重构这段代码？

> 原文：<https://dev.to/flrnd/how-do-you-refactor-this-piece-of-code-5eg6>

各位地鼠朋友，我需要一些见解。我正在为 go 中的 larder.io 书签写一个命令行客户端。我有这个功能:

```
func checkConfigRoute(configDir string) (string, error) {
    usr, err := user.Current()
    if err != nil {
        return nil, err
    }
    route := join(usr.HomeDir, "/", configDir)
    // Check if the directory exist, if not, create it the route.
    if _, err := os.Stat(route); os.IsNotExist(err) {
        if err := os.MkdirAll(route, os.ModePerm); err != nil {
            return nil, err
        }
    }
    return route, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

我需要检查配置文件是否存在，但在此之前，我想检查路由目录是否存在，如果不存在，创建它并返回路由。

我不知道为什么，但它感觉关闭，不知道我是否做错了。你怎么想呢?怎么重构呢？我是否应该将完整的路径直接传递给函数，而不是在内部加入它？非常感谢帮助！

谢谢！

## 更新:

好吧，让我看看。我已经去掉了那个功能，而是重新设计了整个配置设置。

下面是代码:

```
// Config token
// and path
// yaml config file
type Config struct {
    FileName    string
    Path        string
    TokenString string `yaml:"token"`
}

// Token returns the configuration token string
func (c Config) Token(handle []byte) (string, error) {
    err := yaml.Unmarshal(handle, &c)
    if err != nil {
        log.Println("Error: ", err)
    }
    return c.TokenString, err
}

// SetPath defines the configuration directory path
func (c Config) SetPath(condigDir string) {
    c.Path = condigDir
}

func getUsrHomeDir() string {
    usr, err := user.Current()
    if err != nil {
        log.Println("User home error: ", err)
    }
    return join(usr.HomeDir, "/")
}

func firstTimeRun(path string) error {
    // Since we are going to store our api token,
    // for security reasons we don't want give read access
    // to other users in our system.
    if err := os.MkdirAll(path, 0700); err != nil {
        return err
    }

    return nil
}

func readConfig(route string) []byte {
    handle, err := ioutil.ReadFile(route)

    // Most common cases are:
    // Either the file doesn't exist, or
    // Exist but you don't have read permission

    if os.IsNotExist(err) {
        firstTimeRun(route)
    }

    if os.IsPermission(err) || err != nil {
        log.Println("Reading config file: ", err)
        os.Exit(1)
    }
    return handle
} 
```

Enter fullscreen mode Exit fullscreen mode

因为我知道。ReadFile 已经返回任何错误如果 config 文件有问题，我可以直接避开整个 checkConfigRoute，如果有错误我已经知道有问题了。所以我从 readConfig 函数初始化它。

想法？

再次感谢！