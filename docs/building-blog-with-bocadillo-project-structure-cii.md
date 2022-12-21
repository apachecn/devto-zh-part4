# 用 Bocadillo 构建博客-项目结构

> 原文：<https://dev.to/itachiuchiha/building-blog-with-bocadillo-project-structure-cii>

**该系列的 GitHub 库**:【https://github.com/aligoren/bocadillo_blog T2】

* * *

## 项目结构

我们将在我们的项目中做一些改变。首先，我们将创建一个名为 **blog** 的文件夹。因此，这个文件夹将包含我们的项目文件。

使用这种结构，我们可以分离我们的项目。您可以为项目文件夹使用任何名称。

之后，我们会将 **app.py** 文件移动到该文件夹中。

[![](img/51f10c5acada8fc46afa3826d957baa8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PirEhfRM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mlq8vo8ttl7y51cpjr6u.png)

## ASGI 文件

我们将在根文件夹下创建一个名为 **asgi.py** 的文件。

会是那样的；

```
from bocadillo import configure
from blog.app import app

configure(app) 
```

Enter fullscreen mode Exit fullscreen mode

## app.py 文件

我们不需要在这个文件中再次调用 configure 方法。因为我们在 **asgi.py** 文件中调用了它。让我们更改 **blog/app.py** 文件。

```
from bocadillo import App

app = App()

@app.route("/")
async def index(req, res):
    res.text = "Hello World!" 
```

Enter fullscreen mode Exit fullscreen mode

我们刚刚删除了 configure 方法。现在我们可以这样运行我们的项目；

```
uvicorn asgi:app --reload 
```

Enter fullscreen mode Exit fullscreen mode

现在更优雅了:)我觉得这很重要，因为你不想把所有东西都放在一个文件里。没人想要。在大型项目中，一切都可能更加复杂。

接下来的事情将是关于设置。