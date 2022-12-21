# 实用锈网开发- CI 特拉维斯

> 原文：<https://dev.to/werner/practical-rust-web-development-ci-travis-4omf>

像 Travis 这样的工具真的有助于确定我们软件的稳定性，特别是当我们有一个庞大的代码库，需要大量的时间来编译和测试的时候。

在本帖中，我将展示如何为我们的项目集成 Travis CI，因为它是开源的，我们可以使用 Travis 免费帐户。

我们需要执行几件事情，首先我们需要在存储库[页面](https://travis-ci.org/account/repositories)中为 Travis CI 启用我们的项目。然后我们在根目录下添加一个`.travis.yml`文件，配置如下:

`.travis.yml`

```
language: rust
rust:
  - stable
  - beta
  - nightly
matrix:
  allow_failures:
    - rust: nightly
  fast_finish: true
cache: cargo
services:
  - postgresql
addons:
  postgresql: "10"
  apt:
    packages:
      - postgresql-10
      - postgresql-client-10
env:
  global:
    - PGPORT: 5433
before_script:
  - sudo sed -i -e '/local.*peer/s/postgres/all/' -e 's/peer\|md5/trust/g' /etc/postgresql/*/main/pg_hba.conf
  - sudo service postgresql restart
  - sleep 1
  - sudo apt-get update
  - sudo apt-get install -y postgresql-10-rum
  - psql -c 'create database mystore_test;' -U postgres
  - psql -c 'CREATE EXTENSION rum;' -d mystore_test -U postgres
script:
  - cargo install diesel_cli --quiet || true
  - cargo build --verbose --all
  - DATABASE_URL=postgres://postgres:@localhost/mystore_test diesel setup
  - cargo test --verbose --all 
```

虽然我们的目标是稳定的，但我们可能希望对所有编译器目标进行测试，看看我们的代码是否没有破坏一些新功能。

我们启用 postgres v10 并编写测试前需要的脚本。

最后一件事是在自述文件中添加 badge，这样人们可以看到我们的软件是否稳定。

`README.md` :

```
# practical-rust-web
[![Build Status](https://travis-ci.org/practical-rust-web-development/mystore.svg?branch=master)](https://travis-ci.org/practical-rust-web-development/mystore) 
```

你可以在这里看一下源代码