# 在 Firmhouse 的 Kubernetes 中管理应用程序机密

> 原文：<https://dev.to/firmhouse/managing-app-secrets-in-kubernetes-at-firmhouse-eki>

[![Managing app secrets in Kubernetes at Firmhouse](img/a8bf57258b81004daf26d5f7849fde65.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NAqSYOSX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.michielsikkes.com/conteimg/2019/07/shuto-araki-0Nlp0vqSgBY-unsplash.jpg)

在 [Firmhouse](https://www.firmhouse.com) 我们逐渐从基于 [Dokku](https://github.com/dokku/dokku) 的基础设施迁移到运行在 [DigitalOcean Kubernetes](https://www.digitalocean.com/products/kubernetes/) 上的基于 [Kubernetes](https://kubernetes.io) 的新基础设施。不再需要手工劳动。与自动化同行！

在新的设置中，我们非常依赖 [Terraform](https://terraform.io) 作为代码协作和自动化的基础设施。我们向 Terraform 存储库添加的一项内容是为我们的应用部署自动管理机密和环境变量。

在这篇文章中，我将向你展示我们如何通过 Terraform、 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 和 [Kubernetes Secrets](https://kubernetes.io/docs/concepts/configuration/secret/) 来管理秘密和环境变量。

# Kubernetes 中的应用部署

首先，让我们向您展示一下这最终是为了什么:应用程序部署。我们的 [Ruby on Rails](https://rubyonrails.org) 应用程序使用 [Kubernetes 部署](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)进行部署。下面是我们 Terraform 存储库中的一个应用程序的片段: