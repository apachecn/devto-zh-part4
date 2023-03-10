# 如何使用你的？dockerignore 作为白名单

> 原文：<https://dev.to/kevinpollet/how-to-use-your-dockerignore-as-a-whitelist-3b77>

许多工具使用忽略文件来从构建、处理或发布步骤中排除文件(例如，`.npmignore`用于 npm，`.gitignore`用于 Git，`.dockerignore`用于 Docker)。随着项目的增长、发展，可能很难维护排除模式，并且作为副作用，您可能会暴露不需要的或敏感的文件。使用您的`.dockerignore`作为白名单可能是一个避免这些错误并减少更新工作的优雅选择。

## `.dockerignore`是什么意思？

当你创建一个 Docker 映像时，第一步就是在一个`Dockerfile`中定义构建[指令](https://docs.docker.com/engine/reference/builder/)。第二步是从 Docker CLI 运行`docker build PATH`命令。`PATH`参数是包含您的`Dockerfile`的文件夹的路径。因此，Docker CLI 将把构建`context`发送到 Docker 守护进程。

### 造`context`

Docker CLI 将给定的`PATH`中的每个文件和文件夹`tar`发送到 Docker 守护进程。这些文件和文件夹组成了构建`context`。您的 Docker 映像将使用此`context`构建，因此发送到守护程序的不需要的文件可以通过`ADD`或`COPY`指令错误地打包在最终映像中。

此外，Docker 守护进程可以在远程机器上执行，所以最好避免向它发送大的、不必要的和敏感的文件。

记住，`ADD`和`COPY`指令是由守护进程运行的，而不是由 CLI 运行的，所以它们不用于从构建`context`中删除不需要的文件。

### 从构建中排除文件`context`

当您调用`docker build`命令时，Docker CLI 在构建`context`的根文件夹中(即在给定`PATH`的根文件夹中)查找名为`.dockerignore`的文件。如果这个文件存在，那么每个符合[排除模式](https://docs.docker.com/engine/reference/builder/#dockerignore-file)的文件和文件夹都不会包含在发送到 Docker 守护进程的`tar`档案中。Docker 构建会更快，打包不需要的文件的风险也会降低。

例如，如果您想从构建`context`中排除`.git`文件夹和所有的 markdown 文件，将以下内容添加到您的`.dockerignore` :

```
.git
*.md 
```

Enter fullscreen mode Exit fullscreen mode

## 使用你的`.dockerignore`作为白名单

黑名单和白名单是一个长期的争论，我认为，两种类型都有其使用案例。根据我的经验，从构建中排除的文件会比包含的文件多，所以为了保持整洁和安全，将文件加入白名单不容易出错。

如果你读过`.dockerignore` [文档](https://docs.docker.com/engine/reference/builder/#dockerignore-file)，你可能会注意到下面几行:

> 最后，您可能希望指定哪些文件要包含在上下文中，而不是要排除哪些文件。为此，将*指定为第一个模式，后跟一个或多个！异常模式。

相当简单！

例如，下面的`.dockerignore`命令 Docker CLI 从构建`context` :
中排除除了`lib`文件夹中的`JS`文件之外的所有内容

```
# Ignore everything
*

# Allow files and folders with a pattern starting with !
!lib/**/*.js 
```

Enter fullscreen mode Exit fullscreen mode

有了白名单，也有可能从白名单文件夹中发布不想要的或敏感的文件，但在我看来，这个问题是次要的。

## 总结

现在您已经知道如何使用您的`.dockerignore`将文件列入黑名单或白名单，选择最适合您的用例的选项。总之，使用您的`.dockerignore`作为白名单可以:

*   默认情况下从构建中排除所有内容`context`
*   减小发送到 Docker 守护进程的`tar`档案的大小
*   降低在 Docker 中打包不需要或敏感文件的风险`image`
*   减少更新排除模式的工作量

PS:你可以用你的`.gitignore`或者`.npmignore`用同样的招数。对于 npm，最佳实践是使用您的`package.json`中的[文件](https://docs.npmjs.com/files/package.json#files)属性，而不是`.npmignore`文件。