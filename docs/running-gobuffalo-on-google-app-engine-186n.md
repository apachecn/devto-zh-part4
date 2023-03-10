# 在谷歌应用引擎上运行 GoBuffalo

> 原文：<https://dev.to/tombiscan/running-gobuffalo-on-google-app-engine-186n>

想过如何在 GAE 部署大水牛吗？

下面是让您开始的`app.yaml`文件:

```
runtime: go111

#[START env_variables]
env_variables:
  PORT: 8080
  GO_ENV: "production"
#[END env_variables]

handlers:
- url: /robots.txt
  static_files: public/robots.txt
  upload: public/robots.txt
- url: /assets
  static_dir: public/assets
- url: /.*
  script: auto 
```

此外，将您的`.env`文件设置为包含:

```
DATABASE_URL="user=postgres password=Im4P4$$w0Rd dbname=golangtesting_production host=/cloudsql/my-project-id:europe-west1:my-production-instance"
SESSION_SECRET=yeahR1ght 
```

如果您想将其与数据库一起部署。

更多细节请看应用引擎上的 [Gobuffalo。在那里，您可以找到其他环境设置、如何设置数据库、运行迁移和处理资产。](https://golangtesting.com/posts/gobuffalo-app-engine)