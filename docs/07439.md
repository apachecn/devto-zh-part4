# 用 Git 钩子在 DigitalOcean Droplet 上部署代码

> 原文：<https://dev.to/alexmacarthur/deploying-code-with-a-git-hook-on-a-digitalocean-droplet-5h60>

我一直在 Node 中从事一个涉及长时间运行、资源密集型批处理作业的项目。起初，当我的需求比较简单时，我使用 Heroku 来运行这些作业。这很棒，但最终，提供的性价比变得有点太难了，我选择从 Heroku 转移到 DigitalOcean。

几乎立刻，我就被打动了。在短短的一段时间内，我几乎没有遇到任何问题就开始运行这些工作。但是有一个挑战我还没有解决:设置某种部署过程，将代码从我的 Git 存储库放到我的 droplet 中。我被赫罗库宠坏了。他们让这部分工作变得异常轻松。但值得庆幸的是，当我采取行动时，我的需求相对简单:

*   在一次`git push`中，我希望我的代码被复制到我的 droplet 中。
*   在同一次推送中，我想`npm install`我的`package.json`中的依赖项。
*   我想选择控制哪个分支将触发该微滴的部署。

事实证明，设置没有我预期的那么复杂。一个 Git 挂钩和一点本地配置就能满足上面提到的所有需求。这篇文章基本上是我备份并记录了我从实验和谷歌搜索中收集到的所有信息。请注意，我不会进入配置数字海洋或任何其他 VPS 的杂草。出于我在这里展示的目的，只需确保您可以通过 SSH 访问您的 droplet，并且上面安装了 Git。

## 在您的 Droplet 上配置远程存储库

首先，在你的 droplet 上创建一个空的存储库。一个空库是一个没有工作树的库，仅仅用于*共享*代码——而不是使用它。我们将只推进到这个存储库，所以`--bare`是我们要走的路。

对于本例，通过将`ssh` -ing 到您的 droplet 中，创建一个`neat-app-repo`目录，并初始化该存储库。

```
ssh user@your-ip-address
cd /home
mkdir neat-app-repo
cd neat-app-repo
git init --bare 
```

Enter fullscreen mode Exit fullscreen mode

**在新创建的存储库中创建一个`post-receive`钩子文件。**如果你对他们不熟悉，[跟上。](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hook)简而言之，这个钩子将允许在 droplet 上的库*接收到*代码后*做一些事情*(即当我们做一个`git push`。在我们的例子中，在我们的代码被接收到之后，我们所希望发生的就是将它移动到一个不同的目录中，并安装它的依赖项。

如果您`cd`进入您的`neat-app-repo/hooks`目录(这是您初始化空存储库时创建的目录之一)，您应该看到一长串`*.sample` Git 挂钩。当你在那里的时候，创建一个*新的* `post-receive`钩子:

```
touch post-receive 
```

Enter fullscreen mode Exit fullscreen mode

并将以下骨骼粘贴在那里:

```
#!/bin/bash

while read oldrev newrev ref
do
    # We gonna do stuff. 
done 
```

Enter fullscreen mode Exit fullscreen mode

这是一个有三个参数的`bash` `while read`循环:

`oldrev` -推送分支中先前提交的 SHA。

`newrev` -推送分支中新提交的 SHA。

`ref` -刚刚推送的分支的 Git 引用。示例:`refs/heads/master`

在这个块中，我们可以自由地做任何我们想做的事情，比如复制刚刚被推到不同目录的分支:

```
#!/bin/bash

+# Location of our bare repository.
+GIT_DIR="/home/neat-app-repo"
+
+# Where we want to copy our code.
+TARGET="/home/neat-app-deployed"

while read oldrev newrev ref
do
- # We gonna do stuff.
+ # Neat trick to get the branch name of the reference just pushed:
+ BRANCH=$(git rev-parse --symbolic --abbrev-ref $ref)
+
+ # Send a nice message to the machine pushing to this remote repository.
+ echo "Push received! Deploying branch: ${BRANCH}..."
+
+ # "Deploy" the branch we just pushed to a specific directory.
+ git --work-tree=$TARGET --git-dir=$GIT_DIR checkout -f $BRANCH
done 
```

Enter fullscreen mode Exit fullscreen mode

保存之后，在实际使用之前还需要几个步骤:

1.  确保钩子是可执行的。如果你跳过这一步，当你从你的机器推送时，你会得到一个`The 'hooks/post-receive' hook was ignored because it's not set as executable`错误。

```
chmod +x post-receive 
```

Enter fullscreen mode Exit fullscreen mode

1.  确保目标目录确实存在。如果没有，您将得到另一个错误。

```
mkdir /home/neat-app-deployed 
```

Enter fullscreen mode Exit fullscreen mode

现在，出于测试目的，打开本地机器上的存储库，并将`origin`设置为 droplet 上的空存储库所在的位置。

```
git remote add origin root@YOUR_IP_ADDRESS:/home/neat-app-repo 
```

Enter fullscreen mode Exit fullscreen mode

任意提交并给它一个`git push`。如果成功，您应该会看到类似这样的内容。

```
remote: Push received! Deploying branch: master...
remote: Switched to branch 'master' 
```

Enter fullscreen mode Exit fullscreen mode

**现在，让我们(可选地)将新部署限制到特定分支机构。**如果你创建一个新的分支，并把它推到你的 droplet 中，你会看到它成功部署，就像你在`master`上一样。可能不太理想，所以让我们修改我们的钩子，只在主分支被推送时部署。

```
#!/bin/bash

# Location of our bare repository.
GIT_DIR="/home/neat-app-repo"

# Where we want to copy our code.
TARGET="/home/neat-app-deployed"

while read oldrev newrev ref
do
    # Neat trick to get the branch name of the reference just pushed:
    BRANCH=$(git rev-parse --symbolic --abbrev-ref $ref)

+ if [[$BRANCH == "master"]];
+ then
        # Send a nice message to the machine pushing to this remote repository.
        echo "Push received! Deploying branch: ${BRANCH}..."

        # "Deploy" the branch we just pushed to a specific directory.
        git --work-tree=$TARGET --git-dir=$GIT_DIR checkout -f $BRANCH
+ else 
+ echo "Not master branch. Skipping."
+ fi
done 
```

Enter fullscreen mode Exit fullscreen mode

Gr8！现在，由于我的项目在 Node 中，我需要设置最后一件事:**在每个部署上运行`npm install`。**在我的例子中，`nvm`负责指定我运行哪个版本的节点，所以更新我的钩子看起来像这样:

```
#!/bin/bash

# Location of our bare repository.
GIT_DIR="/home/neat-app-repo"

# Where we want to copy our code.
TARGET="/home/neat-app-deployed"

while read oldrev newrev ref
do
    # Neat trick to get the branch name of the reference just pushed:
    BRANCH=$(git rev-parse --symbolic --abbrev-ref $ref)

    if [[$BRANCH == "master"]];
    then
        # Send a nice message to the machine pushing to this remote repository.
        echo "Push received! Deploying branch: ${BRANCH}..."

        # "Deploy" the branch we just pushed to a specific directory.
        git --work-tree=$TARGET --git-dir=$GIT_DIR checkout -f $BRANCH
    else 
        echo "Not master branch. Skipping."
    fi

+ # Source nvm to make it available for use inside this script.
+ . $HOME/.nvm/nvm.sh
+    
+ # Use the LTS version of Node.
+ nvm use --lts
+   
+ # Navigate to where my deployed code lives. 
+ cd /home/neat-app-deployed
+
+ # Install dependencies in production mode.
+ npm install --production
done 
```

Enter fullscreen mode Exit fullscreen mode

这样，推送至我的 droplet 将会把我的代码准确地部署到我想要的位置，并安装它需要运行的依赖项。

## 配置您的本地存储库以便于部署

当我第一次设置好这一切时，我假设如果我想将我的代码推送到一个远程 GitHub 库*并同时部署它，我需要创建两个远程并分别推送它们。然后我看到了这条推特:*

> 直到你可以在同一个遥控器上添加两个不同的 Git repo URLs，一个`git push`就可以把两个都推上来。有时候橘子网还是有用的！[pic.twitter.com/lwjEmj1RAM](https://t.co/lwjEmj1RAM)
> 
> —戴夫·塞德迪亚([@ dceddia](https://dev.to/dceddia))[2019 年 7 月 22 日](https://twitter.com/dceddia/status/1153365976588664833?ref_src=twsrc%5Etfw)

一推，两个目的地。由于我们的`post-receive`钩子只将推送部署到`master`分支，这对于我们的工作流来说是效率和安全性的一个很好的平衡。

不可否认，在本地配置我的 Git 遥控器有点奇怪，但是最终都解决了。注意:这里假设您已经将 DO droplet 设置为您的`origin`。

```
# Change the `fetch` URL, so we always pull code from GitHub.
git remote set-url origin git@github.com:alexmacarthur/neat-app.git

# Re-add `push` URLs, so that we push to GitHub AND DigitalOcean.
git remote set-url --add --push origin root@YOUR_IP_ADDRESS:/home/neat-app-repo
git remote set-url --add --push origin git@github.com:alexmacarthur/neat-app.git 
```

Enter fullscreen mode Exit fullscreen mode

最后`git remote -v`返回这个:

```
origin root@YOUR_IP_ADDRESS:/home/neat-app-repo (fetch)
origin git@github.com:alexmacarthur/neat-app.git (push)
origin root@YOUR_IP_ADDRESS:/home/neat-app-repo (push) 
```

Enter fullscreen mode Exit fullscreen mode

配置好之后，一个简单的`git push`将你的代码发送到两个不同的遥控器，也许每天可以节省你几秒钟。

## 期待抓到你

虽然这在概念上可能很简单，但处理所有这些细节时不时会让我感到沮丧，尤其是因为这是我第一次完成这样的事情。也就是说，在处理不可避免会出现的问题时，给自己一点宽限。希望你在这里读到的能帮助减轻这个过程中的痛苦，哪怕只是一点点。如果这是真的，把这些都写出来是值得的。