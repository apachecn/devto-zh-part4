# 从 BitBucket 差异部署到亚马逊 S3

> 原文：<https://dev.to/gmoretti/differential-deploy-to-amazon-s3-from-bitbucket-5ek3>

这是我在 DEV.to 上的第一篇帖子，很高兴能参与进来，也很高兴能提升这些写作技巧。

在自动化静态文件部署的想法下，作为向 CI/CD 环境迈进的一步，并利用我们最近将所有静态文件转移到 AWS S3 存储桶的事实，我决定尝试自动化我们的过程。

## 当前状态

我们的部署流程(集成流程)有两大元素需要部署:一个 Salesforce 包。以及一个包含 Salesforce 包使用的所有静态文件(包括前端代码)的文件夹。

创建此 Salesforce 包时，存储库标记有一个 *TEST_X* 名称。

**目前部署静校正量的过程**是这样的:

1.  转到源树。
2.  检查在当前标记和最后一个标记之间发生变化的文件。
3.  将这些文件复制到 AWS S3
4.  使 Cloudfire 缓存无效(亚马逊的 CDN...有点)

一旦完成了第一个大的部署，也可能发生只需要静态部署的小的变化被单独部署。而不需要生成另一个包。

## 新方法

使用位桶管道来执行我们刚刚描述的脚本。

AWS S3 已经有一个非常好的渠道可以使用。唯一的缺点是，用于将文件复制到 S3 的 sync 命令使用时间戳和文件大小的组合来决定是否上传。因为在 BitBucket 中，管道是从 docker 容器执行的，所以从 GIT 克隆的文件总是比 S3 上的文件有一个更新的时间戳，这意味着无论如何所有的文件都会被复制。

该脚本将做的唯一新的事情是:

1.  使用 GIT diff 获取改变的文件(如果我们在一个标签中或者没有改变行为)
2.  AWS CLI 发送特定文件
3.  AWS CLI 以编程方式使缓存无效

### 简单的比特斗管道

第一步是创建一个管道，我们将从这里执行脚本。我认为官方文件在这件事上做得很好。记住我们将使用**简单管道**来保持文件和代码最少。

我从 bitbucket 文档中错过的唯一部分是，它让您认为不可能从简单管道访问存储库定义的变量。这是对的一半，因为当使用项目的 bitbucket-pipelines.yml 文件中的管道时，您实际上可以传递它们。

#### Dockerfile

这些说明留给你一个三个文件的项目。让我们从 Dockerfile 文件开始。

```
FROM atlassian/pipelines-awscli:1.16.185
RUN apk update && apk add ca-certificates && rm -rf /var/cache/apk/*
COPY pipe.sh /
ENTRYPOINT ["/pipe.sh"] 
```

与提供的唯一不同之处是第一行

> `FROM atlassian/pipelines-awscli:1.16.185`

这为我们提供了预安装的 AWS CLI 工具，因此我们不必在构建时这样做。

#### 管道脚本

接下来，我们有将用作管道的脚本，它将包含 GIT 命令、部署和缓存失效。
我在这篇文章的[中披露了这个脚本的主要内容。让我们看一看。](https://www.lambrospetrou.com/articles/aws-s3-sync-git-status/) 

```
#!/usr/bin/env bash
#TODO: Apply colors and errors with commons.sh
set -ex

# mandatory parameters
# path variables should not contain slashes
AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID:?'AWS_ACCESS_KEY_ID variable missing.'}
AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY:?'AWS_SECRET_ACCESS_KEY variable missing.'}
S3_BUCKET=${S3_BUCKET:?'S3_BUCKET variable missing.'}
LOCAL_PATH=${LOCAL_PATH:?'LOCAL_PATH variable missing.'}
TAG_REGEX=${TAG_REGEX:?'TAG_REGEX variable missing.'}
CLOUDFRONT_DISTRIBUTION_ID=${CLOUDFRONT_DISTRIBUTION_ID:?'CLOUDFRONT_DISTRIBUTION_ID variable missing.'}

echo "Starting deployment..."
echo "AWS_ACCESS_KEY_ID is: ${AWS_ACCESS_KEY_ID}"
echo "S3_BUCKET is: ${S3_BUCKET}"
echo "LOCAL_PATH is: ${LOCAL_PATH}"
echo "TAG_REGEX: ${TAG_REGEX}"

# This will give us the last two tags created that comply with the TAG_REGEX parameter
TAGS=()
for i in $( git tag -l --sort=refname "${TAG_REGEX}" | tail -2 ); do TAGS+=( "$i" )
done echo "Checking difference between tags:"
echo "${TAGS[0]}"
echo "${TAGS[1]}"

# We will get the list of files that changed between those two tags but keep only the ones that
# begin with the path we want to deploy
FILES=()
for i in $( git diff ${TAGS[0]} ${TAGS[1]} --name-only | sed -n "s|${LOCAL_PATH}/||p" ); do FILES+=( "$i" )
done echo "Files to be deployed..."
printf '%s\n' "${FILES[@]}"

# Construct the parameters to the AWS CLI for the files to includes
CMDS=()
for i in "${FILES[@]}"; do CMDS+=("--include=$i""*")
done echo "${CMDS[@]}"

# Exclude ALL diles and only includes the previously defined.
# Remove dryrun to deploy to aws s3
# Remember this command won't show any output if the file in the bucket is MORE RECENT than the one in local
echo "${CMDS[@]}" | xargs aws s3 sync ./${LOCAL_PATH}/ s3://${S3_BUCKET}/ --dryrun --delete --exclude "*"

aws cloudfront create-invalidation --distribution-id ${CLOUDFRONT_DISTRIBUTION_ID} --paths /folderToInvalidate/* 
```

一开始的参数部分更多的是提醒我们在脚本中会有什么，这些变量将来自使用管道时的配置。值得提醒你的是，所有的路径都不应该包含斜线，这一点很重要，因为 AWS S3 不允许双斜线。

我们列出了已更改的文件，从 aws sync 命令中排除所有文件，只包括已更改的文件。我们最终使 cloudfire 上的目录缓存无效，以便能够在需要时看到更改。

最后但同样重要的是，在将这个文件推送到回购之前，执行一个

```
chmod -x pipe.sh 
```

在 docker 中有一次执行权限。

#### bitbucket-pipelines.yml

这个文件配置您的管道到 dockerhub 的上传，只是不要忘记在您的 repo
中提供 docker hub 凭证变量。

### 项目用静态文件

现在，我们为项目(bitbucket repo)配置管道文件，以使用新管道并传递必要的变量。
我的`bitbucket-pipelines.yml`文件是这样的:

```
# This is a sample build configuration for Other.
# Check our guides at https://confluence.atlassian.com/x/5Q4SMw for more examples.
# Only use spaces to indent your .yml configuration.
# -----
# You can specify a custom docker image from Docker Hub as your build environment.
image: atlassian/default-image:2

pipelines:
  custom:
    deploy-statics-diff-last-tags:
      - step:
          script:
            - pipe: docker://gmoretti/aws-deploy-diff-last-tag-simple-pipe:latest
              variables:
                AWS_ACCESS_KEY_ID: '$AWS_ACCESS_KEY_ID'
                AWS_SECRET_ACCESS_KEY: '$AWS_SECRET_ACCESS_KEY'
                S3_BUCKET: 'aws-static-deploy-test'
                LOCAL_PATH: 'static'
                TAG_REGEX: 'TEST_*' 
```

让我们一行一行地来，首先我们声明这是一个`custom`步骤，所以要使用它，我们需要转到 bitbucket 分支并手动点击它，下一行是步骤的**名称，它将出现在可以执行的事情列表中。** 

```
custom:
    deploy-statics-diff-last-tags: 
```

接下来，dockerhub 中管道的 url。

```
 - pipe: docker://gmoretti/aws-deploy-diff-last-tag-simple-pipe:latest 
```

现在，我们有了 AWS 凭证，您可以在这里轻松地硬编码它们，但是它们作为存储库变量更好，特别是现在 bitbucket 允许您在每个环境中有不同的凭证。

```
 AWS_ACCESS_KEY_ID: '$AWS_ACCESS_KEY_ID'
            AWS_SECRET_ACCESS_KEY: '$AWS_SECRET_ACCESS_KEY' 
```

接下来，没有协议`S3://` **的目标桶，也没有任何结尾**的斜线。您可以将**目标路径**添加到您想要文件所在的特定文件夹中。

```
 S3_BUCKET: 'aws-static-deploy-test' 
```

因为我们在项目的根目录中，所以我们将路径放在我们想要部署的目录中。同样，开头和结尾没有斜线。

```
 LOCAL_PATH: 'static' 
```

最后一个变量将帮助我们匹配我们想要比较的标签，所以它们需要在数字上保持一定的连续性。

```
 TAG_REGEX: 'TEST_*' 
```

这将匹配所有标签，如`TEST_1`、`TEST_3`、`TEST_6`等。

### 执行

现在，我们只需要**将所有的变更**推送到 bitbucket 中的**分支**和*执行管道*。

## 最后的想法

虽然要解决这个特定的情况，这个管道会工作得很好，但问题是还有其他几个没有涉及的情况，比如，部署一个标签没有涉及的文件，或者在几个标签之间提交文件。虽然所有这些事情都可以通过扩展管道脚本来完成，但事实是**仅仅标记和部署所有静态文件将会消除整个问题**，并会像使用 bitbucket *AWS S3 维护管道*一样离开。

这就是我今天要带回家的。

## 参考文献

*   [BitBucket 管道教程](https://confluence.atlassian.com/bitbucket/how-to-write-a-pipe-for-bitbucket-pipelines-966051288.html)

*   [AWS S3 仅同步修改的文件，使用 Lambros Petrou 的 git 状态](https://www.lambrospetrou.com/articles/aws-s3-sync-git-status/)

如果您有任何想法，请随时联系我，**谢谢！**