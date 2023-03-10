# KubeRider::与 TravisCI 一起建造

> 原文：<https://dev.to/namuan/kuberider-building-with-travisci-2nh4>

[![](img/e9da3848c8f2c44f5f138a8748e6c25a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eVRQEGoX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://deskriders.dev/media/posts/3/travis_ci_build_finished.png)

下面是. travis.yml 文件在 KubeRider
中的样子

```
language: python
os: linux
dist: trusty
sudo: required
python: '3.6'
git:
 depth: 1
branches:
 only:
 - master
env:
 global:
 - BUILD\_VERSION="0.0.1"
script:
 - git config --local user.name "namuan"
 - git remote set-url origin https://namuan:${GITHUB\_TOKEN}@github.com/namuan/kube-rider.git
 - git push origin :refs/tags/${BUILD\_VERSION}
 - git tag -f -am v${BUILD\_VERSION} ${BUILD\_VERSION}
 - git push origin ${BUILD\_VERSION}
notifications:
 email: false 
```

一旦添加到项目中，下一步就是将项目添加到 TravisCI 中。

[![](img/a21a2ee8d4779c957f90fd73d695a5dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KixTXaca--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://deskriders.dev/media/posts/3/travis_ci_select_repo.png)

几秒钟后，如果设置正确，您将进入下一页，在这里您可以手动触发构建

[![](img/e96d491c1447cb9672a12f8d6ec56fc8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5Q-1B_Tl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://deskriders.dev/media/posts/3/travis_ci_trigger_build.png)

这只是为了看看构建是否正常工作，通常它一检测到 Github 中的提交就会自动触发构建。

[![](img/b95c9943ea8f96dc8419b8732e412feb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---vUjJs5h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://deskriders.dev/media/posts/3/travis_ci_running_build.png)

一旦构建成功，它将在 Github 中用当前版本标记构建版本

[![](img/20a143896a8b859b9eb6162505181e36.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--abqK5aMW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://deskriders.dev/media/posts/3/travis_ci_tag_build.png)