# 使用 Gitlab-CI 自动化 VSCode 扩展发布和变更日志生成

> 原文：<https://dev.to/jhechtf/automating-vscode-extension-publishing-and-changelog-generation-with-gitlab-ci-3pep>

当我构建我的第一个真正的 VSCode 扩展时，我每次都手工发布它。这也意味着我每次都在修改日志。我粗略地研究了我需要做些什么来使 changelog / marketplace 的发布自动化。我从来没有抽出时间来做这件事，因为它看起来比我所拥有的时间要多，而且因为它“只是一个扩展，我可能不会经常更新它

 *在创建了 [Git Angular](https://marketplace.visualstudio.com/items?itemName=Jhecht.git-angular) 之后，我决定将它用作两件事的测试平台:摆弄[标准版](https://github.com/conventional-changelog/standard-version)和最终巩固一种从 Gitlab CI 发布扩展的方法。

# 备注:

标准版本使用角度式提交来创建变更日志。这意味着，如果你来自不同的提交风格，比如表情符号提交，或者只是“提交中任何相关的信息”，那么这部分将会很困难。还有其他预设，但我从来没有使用过它们，所以我不太了解它们或它们如何。我正在考虑为 Git 表情符号 commit 风格创建一个，但不要屏息等待它何时可用。

此外，如果你像我一样，你在寻找一个“优雅”的解决方案，这不会是它。这是我为了让它(或多或少)自动化而不得不做的事情，我认为值得将这个过程记录下来，让其他人以后看到。

# Tl；博士:

1.  确保您从 Gitlab(在“用户设置”>“访问令牌”下创建)和 Azure Devops(此步骤的说明链接如下)都拥有个人访问令牌(PAT)。您的 Gitlab PAT 应该具有写存储库访问权限。我建议让这些东西大约每个月到期一次，以防万一被曝光。当然，你必须记得在一个月内重新制作令牌，但另一种选择是有人劫持一切。
2.  将这两个标记添加到 Settings > CI/CD >变量中。我把 Azure 令牌 PAT 和 Gitlab PAT 命名为 GL_PAT。你可以给它们起任何名字，但是记住你以后给它们起的名字。
    *   确保 package.json 中的“publisher”值没有被采用，否则您的部署会有问题。
3.  复制[。gitlab-ci.yml](#gitlabciyml) 文件，编辑存储库 URL。

# 第一件事:做延伸。

显然我有 Git Angular 可以使用，但是如果你还没有进行扩展，那么你应该使用[向导](https://code.visualstudio.com/api/get-started/your-first-extension)。基本上你会想要安装`yo`和`generator-code`。然后运行`yo code`,按照提示进行操作。如果你不知道你想要这个扩展是什么样的巫术，那也没关系。只要确保你遵循了我在上面链接的标准版本页面中提到的提交模式。它让你的大部分提交看起来像`feat(settings): added in settings page`或`fix(submodule): fixed logic error in submodule that would cause errors`。

# 第二件事:准备 Gitlab 回购

去你的 Gitlab 做一个新的空白回购。进入您的扩展目录，并确保运行

```
# only run this command if you didn't already initiate the folder as a git repo
git init 
# Change "YOUR-USERNAME" and "YOUR-REPO" to their respective values
git remote add https://gitlab.com/YOUR-USERNAME/YOUR-REPO.git origin 
```

对于我的 Git 角度扩展，这将是

```
git remote add https://gitlab.com/jhechtf/git-angular.git origin 
```

## VSCode 市场访问令牌

创建可用于发布到 VSCode Marketplace 的 PAT 的说明有点复杂，我不想在这里深入讨论。请查看本指南。确保你把那个**暂时**复制/粘贴到你电脑的某个地方。我建议您仅对单个 VSCode 扩展使用此令牌，这样，如果在关闭更改时，此令牌暴露，您可以从您的帐户查看活动，并仅撤销必要的令牌。

## Gitlab 个人访问令牌

您可以通过登录 Gitlab，然后点击右侧的个人资料图片来制作新的个人访问令牌。从下拉菜单中点击“设置”

[![](img/7be10f79ab13a23947c78186319bd77f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hi1T2rbk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/56gkunb4m4wos84cx126.PNG)

请确保将您的个人访问令牌命名为您可以快速区分的名称，我建议将到期日期设置在一个月左右。您需要确保您的 PAT 对您的存储库有写权限，否则这将不起作用。

[![](img/40bdbd33bde4a1385740461981675924.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9PveaH7i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/08ng5i1wrp53quzwtfsu.PNG)

# [T1。gitlab-ci.yml](#gitlabciyml)

如果您不确定`.gitlab-ci.yml`文件的结构，请[在此](https://docs.gitlab.com/ee/ci/yaml/)查看大致版本。我已经在下面评论了我的 gitlab-ci 文件，但是让我们来谈谈为了能够推送这些文件，到底需要发生什么。它的基础是

1.  确保 git 已配置，因为`standard-version`自动提交其对`package.json`和`CHANGELOG.md`的更改，如果没有这两个`git configure`行，作业将失败并出现错误，因为 git 不会让未知人员提交。
2.  我使用 yarn，但是等效的 npm 命令将`npm run release`。这从我的`package.json`运行`standard-version`
3.  Gitlab CI 检查提交，而不是分支。因此，在发布脚本中提交的更改将不会被保存。因此，我们必须将这些变更合并回主分支。
4.  将新的提交合并到主分支中，并使用我们的 PAT 将代码向上推。

我制作的非常基本的 Gitlab CI 文件是这样的:

```
# Smaller image, faster to download than the regular node:12 image.
image: node:12-alpine
# Stages list. I will eventually have more stages, but this was a proof-of-concept more than anything
stages:
  - test
  - deploy
# Before any job, add the vsce globally, and install our node_modules.
before_script:
  - yarn global add vsce
  - yarn

Deploy:
  script:
    # update our distro and add git.
    - apk update && apk add git
    # configure our newly-installed git to use our user name and email
    - git config user.email $GITLAB_USER_EMAIL
    - git config user.name $GITLAB_USER_NAME
    # I added a release task which simply runs the standard-version library.
    - yarn release
    # Gitlab checks out the commit, not the branch, putting any committed changes into a detatched-head scenario.
    # this gets the commit SHA and stores it in the $CUR_HEAD variable.
    - CUR_HEAD=$(git rev-parse HEAD)
    # checkout master and merge in our detatched head
    - git checkout master && git merge $CUR_HEAD
    # GITLAB_USER_LOGIN is your gitlab user name, and GL_PAT will be the Gitlab Personal Access Token you created earlier. We are pushing our current HEAD to the master branch
    # on the quoted repo URL
    - git push --follow-tags "https://${GITLAB_USER_LOGIN}:${GL_PAT}@gitlab.com/jhechtf/git-angular.git" HEAD:master
    # finally, publish our extension by passing in our $PAT token.
    - vsce publish -p $PAT

  only:
    - master
  when: manual 
```

# 其他注意事项

我已经修改了我的存储库，因此只有我可以推送到主分支(开发人员可以合并分支，但只有我可以直接推送到主分支)。

`when: manual`使得我必须进入并手动启动部署作业，这样像发布标签这样的东西就不会运行。我可能会保留它，因为一次推送理论上可以产生一百万次不同的补丁推送(这是我不想要的)。

# 期末备注

我乐于听到其他人如何设置他们自己的扩展发布管道，所以如果你有一个设置，请在评论中让我知道。我也希望 Azure Devops 仪表板允许我将 pat 限制在特定项目，但不幸的是，这似乎是不可能的。

如果你有任何需要我澄清的地方，请告诉我！*