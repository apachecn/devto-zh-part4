# 使用 gitlab ci 部署到 google kubernetes 引擎

> 原文：<https://dev.to/rhanarion/deploy-to-google-kubernetes-engine-using-gitlab-ci-42gb>

在之前的一篇博文中，我展示了如何在 gitlab ci 上构建和发布 docker 映像([在 gitlab ci 上构建 docker 映像](https://rhazn.com/posts/build-a-docker-image-on-gitlab-ci-and-publish-it-to-google-container-registry/))

请务必先阅读该帖子，了解概述和权限设置。

# 用新的 docker 映像更新 kubernetes 服务

您可以使用 google 自己的 cloud SDK docker 映像轻松设置部署步骤。注意，具有更改 kubernetes 设置权限的服务帐户在此处保存为“GCLOUD_K8S_KEY”变量。

这项工作改变了我的应用程序部署的形象。您需要将脚本中的最后一行更改为您希望在部署时对 kubernetes 设置进行的任何更改。

```
deploy:
  stage: deploy
  image: google/cloud-sdk:257.0.0
  script:
    - echo $GCLOUD_K8S_KEY | base64 -d > ${HOME}/gcloud-k8s-key.json
    - gcloud auth activate-service-account --key-file ${HOME}/gcloud-k8s-key.json
    - gcloud config set project personal-cloud-project-id
    - gcloud config set compute/zone your-compute-zone
    - gcloud container clusters get-credentials production
    - kubectl set image deployment/???-app ???-app=eu.gcr.io/docker-project-id/app:${CI_COMMIT_SHA}
  only:
    - master
  when: manual 
```

Enter fullscreen mode Exit fullscreen mode

# 关于我

我是一个全栈开发者和数字产品爱好者，我可以自由工作，并总是在寻找下一个令人兴奋的项目:)。

你可以通过电子邮件(pheltweg@gmail.com 或者推特 https://twitter.com/rhanarion T2 联系我。