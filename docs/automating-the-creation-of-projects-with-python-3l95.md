# 用 Python 自动创建项目

> 原文：<https://dev.to/mmphego/automating-the-creation-of-projects-with-python-3l95>

[![image](img/9cde716b528574a36370d8f4b00e9c13.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qWRkeyYP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/mmphego/mmphego.github.io/26c41d503c9237dd2de38efc605652b61899d7e0/assets/2019-07-06-Automating-the-creation-of-projects-with-Python.jpg)

*最初发布于[blog.mphomphego.co.za](https://blog.mphomphego.co.za/)2019 年 7 月 06 日。*

* * *

# 故事

作为一个总是有新的应用/包想法的开发者，我厌倦了每次都做同样的事情——创建项目目录、`README.md`和额外文件的整个任务。我太习惯重复了，以至于它只是在我的潜意识里，直到我受够了。

因此在这篇文章中，我将试着详述我在[我的点文件](http://bit.ly/2FR2AEt)中添加的一个技巧。该函数/技巧自动化了创建新项目的过程，目前仅支持 Python、MicroPython 和 Arduino 项目。

过去，我写了一篇文章，详细介绍了我如何使用我的点文件来提高我的工作效率，点击这里查看

**免责声明:**
如果你想给这些 dotfiles 一个尝试，你应该先分叉这个库，
审查代码，去掉你不想要的或者不需要的东西。不要盲目使用我的设置，除非你知道那意味着什么。

# 该如何如何

我使用了 [cookiecutter](https://github.com/audreyr/cookiecutter) 一个 CLI 实用程序，为 Python 和 MicroPython 项目从模板创建项目，为 Arduino 相关项目使用了 [Platformio](https://platformio.org/) 。成功创建项目后，该函数执行一个 Python 脚本，该脚本使用 [PyGithub](https://github.com/PyGithub/PyGithub/) 在 Github 上创建一个存储库，然后创建一个自动提交，并将我的更改推送到 Github。最后在 [sublime text](https://www.sublimetext.com/) 中打开项目文件夹，让我继续工作。

# 走查

首先，让我们安装 cookiecutter 和 pygithub
`pip install -U cookiecutter pygithub`

## PyGitHub

一旦安装了 pygithub，您可以生成一个令牌或者使用您的用户名和密码。
如果您选择使用代币，请前往[此处](https://github.com/settings/tokens/new)生成代币。

一旦你有了令牌，我们就可以使用 [pygithub](https://github.com/PyGithub/PyGithub/) 测试与 GitHub 的连接。这应该会列出您所有的存储库。

### 用法

在您的 python 环境中运行命令:

```
from github import Github

# First create a Github instance: 
# using username and password g = Github("user", "password")
# or using an access token g = Github("access_token")

# Then play with your Github objects: for repo in g.get_user().get_repos():
    print(repo.name) 
```

Enter fullscreen mode Exit fullscreen mode

## 厨师

现在我们已经设置好了 [PyGithub](https://github.com/PyGithub/PyGithub/) ，我们可以测试 [cookiecutter](https://github.com/audreyr/cookiecutter) 了。这里有一个备餐室，里面有很多 cookiecutters 模板供你选择，你可以在这里[找到它们](https://github.com/cookiecutter/cookiecutter#a-pantry-full-of-cookiecutters)

我亲自为一个 Python 包分叉了 [Cookiecutter 模板。](https://github.com/audreyr/cookiecutter-pypackage)，并根据我的需求进行了修改。

我添加了一些功能，例如:

*   使用`unittest`和/或`py.test`测试设置
*   Travis-CI :准备好进行 Travis 持续集成测试
*   代码质量自动化
*   [Github 变更日志生成器](https://github.com/mmphego/my-dockerfiles/tree/master/git-changelog-generator):轻松生成变更日志。
*   一些自述徽章等。

如果你喜欢使用我的模板，请点击这里:[cookiecutter-python-package](http://bit.ly/30bylje)模板

### 用法

运行 cookiecutter，这将创建一个包含您需要的所有内容的 Python 框架包。按照提示操作。

`cookiecutter https://github.com/mmphego/cookiecutter-python-package`

# 实现

下面你会发现 bash-function 实现可以轻松创建我的项目，它是不言自明的。

```
create_project () {
# Easily create a project x in current dir using cookiecutter templates

    PACKAGES=("pygithub" "cookiecutter" "platformio")
    PACKAGE_DIR=""
    export DESCRIPTION="description goes here!"
    PYTHON2_PIP="python2 -W ignore::DeprecationWarning -m pip -q --disable-pip-version-check"
    PYTHON3_PIP="python3 -W ignore::DeprecationWarning -m pip -q --disable-pip-version-check"
    IDE="subl"

    for pkg in "${PACKAGES[@]}"; do
        if ! ${PYTHON3_PIP} freeze | grep -i "${pkg}" >/dev/null 2>&1; then
            ${PYTHON3_PIP} install -q --user "${pkg}" >/dev/null 2>&1;
        elif ! ${PYTHON2_PIP} freeze | grep -i "${pkg}" >/dev/null 2>&1; then
            ${PYTHON3_PIP} install -q --user "${pkg}" >/dev/null 2>&1;
        fi
    done read -p "What is the language you using for the  (or type of) project? " LANG
    if [[ "${LANG}" =~ ^([pP])$thon ]]; then gecho "Lets build your Python project, Please follow the prompts."
        cookiecutter https://github.com/mmphego/cookiecutter-python-package
        PACKAGE_DIR=$(ls -tr --color='never' | tail -n1)
        export DESCRIPTION=$(grep -oP '(?<=DESCRIPTION = ).*' setup.py)
        cd -- "${PACKAGE_DIR}"
    elif [[ "${LANG}" =~ ^([uU])$python ]]; then gecho "Lets build your Micropython project, Please follow the prompts."
        cookiecutter https://github.com/mmphego/cookiecutter-micropython
        PACKAGE_DIR=$(ls -tr --color='never' | tail -n1)
        cd -- "${PACKAGE_DIR}"
        export DESCRIPTION=$(grep -oP '(?<=DESCRIPTION = ).*' setup.py)
    elif [[ "${LANG}" =~ ^([Aa])$duino ]]; then gecho "Lets build your Arduino project, Please follow the prompts."
        read -p "Enter name of the project directory? " PACKAGE_DIR
        read -p "Enter type of board (nodemcu/uno)? " BOARD
        read -p "What IDE would you like to use vscode/atom/vim? " IDE
        read -p "Enter the description of the project? " DESCRIPTION
        export DESCRIPTION="${DESCRIPTION}"
        mkdir -p "${PACKAGE_DIR}"
        python2 -m platformio init -s -d ${PACKAGE_DIR} -b ${BOARD} --ide ${IDE}
        cd -- "${PACKAGE_DIR}"
    fi

############################################################

    if [[ "${PACKAGE_DIR}" == "$(basename $(pwd))" ]];then git init -q

        python3 -c """
from configparser import ConfigParser
from pathlib import Path
from os import getenv
from subprocess import check_output

from github import Github

try:
    token = check_output(['git', 'config', 'user.token'])
    token = token.strip().decode() if type(token) == bytes else token.strip()
except Exception:
    p = pathlib.Path('.gitconfig.private')
    config.read(p.absolute())
    token = config['user']['token']

proj_name = Path.cwd().name
g = Github(token)
user = g.get_user()
user.create_repo(
    proj_name,
    description=getenv('DESCRIPTION', 'Description goes here!'),
    has_wiki=False,
    has_issues=True,
    auto_init=False)
print('Successfully created repository %s' % proj_name)
"""

        git add .  > /dev/null 2>&1
        git commit -q -nm "Automated commit" > /dev/null 2>&1
        git remote add origin "git@github.com:$(git config user.username)/${PACKAGE_DIR}.git" > /dev/null 2>&1
        git push -q -u origin master > /dev/null 2>&1
        "${IDE}" .
    fi
} 
```

Enter fullscreen mode Exit fullscreen mode

# 演示

此外，我已经挑战自己一个月至少两次用视频记录我的工作(我很乐观)。下面是我做的一个演示，展示了我的函数实现和代码遍历，所以请订阅并打破通知铃声，这样您就可以在内容新鲜的时候获得内容。

[https://www.youtube.com/embed/oy4EqUjabrE](https://www.youtube.com/embed/oy4EqUjabrE)

# 引用

*   [我的点文件](http://bit.ly/2FR2AEt)
*   [直接链接到`create_project`功能](http://bit.ly/2xAG7qB)
*   烹饪刀
*   [PyGithub](https://github.com/PyGithub/PyGithub/)
*   [Codacy](https://app.codacy.com/)