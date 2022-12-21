# 介绍 Furnace——用于管理应用程序和基础设施生命周期的轻量级解决方案

> 原文：<https://dev.to/skarlso/introducing-furnace-a-lightweight-solution-for-managing-your-application-and-infrastructure-lifecycle-5anf>

# 简介

大家好！

今天，我将向您展示如何通过一个名为 [Furnace](https://github.com/go-furnace/go-furnace) 的 CLI 来管理应用的基础设施需求和部署生命周期。

我们开始吧，好吗？

# 什么是熔炉？

要回答这个问题，我们必须首先了解什么是 AWS CloudFormation 和 GCP 部署管理器。

简而言之:

## AWS 云的形成

AWS [CloudFormation](https://aws.amazon.com/cloudformation/) 提供了一种使用基于 YAML 或 JSON 的配置实体来描述基础设施的方法。它创建了一个堆栈，可以用一种很好的、分组的简洁方式处理资源。让我们来看一个例子(更多可以在这里找到:[云形成模板例子](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/gettingstarted.templatebasics.html) :

```
Parameters:
  KeyName:
    Description: The EC2 Key Pair to allow SSH access to the instance
    Type: 'AWS::EC2::KeyPair::KeyName'
Resources:
  Ec2Instance:
    Type: 'AWS::EC2::Instance'
    Properties:
      SecurityGroups:
        - !Ref InstanceSecurityGroup
        - MyExistingSecurityGroup
      KeyName: !Ref KeyName
      ImageId: ami-7a11e213
  InstanceSecurityGroup:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      GroupDescription: Enable SSH access via port 22
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: '22'
          ToPort: '22'
          CidrIp: 0.0.0.0/0 
```

这里发生了很多事情，但是读了一点之后，我们会习惯的。上面的模板创建了一个基本的 EC2 实例，其中一个安全组允许基于密钥的 SSH 访问。它定义了一个名为`KeyName`的参数，我们稍后可以在 CLI 中提供这个参数。KeyName 是创建的 EC2 实例将用于访问的 SSH 密钥的名称。它必须已经存在于使用的帐户中。

注意这个:`!Ref KeyName`。这是模板中的函数调用。这一点以及更多的问题使得 CloudFormation 模板如此强大。有条件句、if 和复句。映射、数组和静态变量。它允许创建动态基础架构模板，为不同的场景创建不同的解决方案。

## GCP 部署经理

GCP 有一个类似的服务叫做部署管理器。基本上做同样的事情，但是有不同的想法。它使用 [Jinja2](https://jinja.palletsprojects.com/en/2.10.x/) 模板来创建动态基础设施。它更加强大，因为它允许在模板文件或实际的 Python 文件中提供完整的 Python 支持。还定义绑定参数的模式文件。因此，您可以创建一个 yaml 模板，它使用带有变量的 Jinja 模板，然后定义一个包含所述变量的 json 或 yaml 文件，并自动完成整个过程，而无需在 cli 中输入任何内容。

让我们看一个例子(这里有很多: [GCP 部署管理器模板](https://github.com/GoogleCloudPlatform/deploymentmanager-samples)):

主 YAML 驱动:

```
imports:
- path: cloudbuild.jinja

resources:
- name: build
  type: cloudbuild.jinja
  properties:
    resourceToList: deployments 
```

包含逻辑并使用 yaml 属性的 JINJA 模板:

```
resources:
- name: build-something
  action: gcp-types/cloudbuild-v1:cloudbuild.projects.builds.create
  metadata:
    runtimePolicy:
    - UPDATE_ALWAYS
  properties:
    steps:
    - name: gcr.io/cloud-builders/gcloud
      args:
      - deployment-manager
      - {{ properties['resourceToList'] }}
      - list
    timeout: 120s 
```

这可能会变得非常复杂，尤其是在有多个文件的情况下。这需要一点学习，以获得正确的和理解模板之间的联系。

## 熔炉

那么熔炉在这一切中扮演什么角色呢？

Furnace 提供了处理这些部署的简单方法。它基本上为这两者中的任何一个提供了一个 CRUD cli，在这个过程中使用了最少的配置。它是轻量级的幕后工具，可以将应用程序部署到这些环境中。对于 AWS，它使用 CodeDeploy 来实现这一点，对于 GCP，它使用部署管理器，因为坦率地说，在 GCP 定义代码部署非常简单。

让我们来看看这两种情况:

### AWS 代码部署

假设您有一个想要部署 web 应用程序的 EC2 实例。最简单的云形成模板应该是这样的:

```
{  "AWSTemplateFormatVersion":  "2010-09-09",  "Description":  "AWS CloudFormation Sample Template EC2InstanceWithSecurityGroupSample: Create an Amazon EC2 instance running the Amazon Linux AMI. The AMI is chosen based on the region in which the stack is run. This example creates an EC2 security group for the instance to give you SSH access. **WARNING** This template creates an Amazon EC2 instance. You will be billed for the AWS resources used if you create a stack from this template.",  "Parameters":  {  "KeyName":  {  "Description":  "Name of an existing EC2 KeyPair to enable SSH access to the instance",  "Type":  "AWS::EC2::KeyPair::KeyName",  "ConstraintDescription":  "must be the name of an existing EC2 KeyPair.",  "Default":  "NonExisting"  },  "SSHLocation":  {  "Description":  "The IP address range that can be used to SSH to the EC2 instances",  "Type":  "String",  "MinLength":  "9",  "MaxLength":  "18",  "Default":  "0.0.0.0/0",  "AllowedPattern":  "(\\d{1,3})\\.(\\d{1,3})\\.(\\d{1,3})\\.(\\d{1,3})/(\\d{1,2})",  "ConstraintDescription":  "must be a valid IP CIDR range of the form x.x.x.x/x."  }  },  "Resources":  {  "InstanceProfile":  {  "Type":  "AWS::IAM::InstanceProfile",  "Properties":  {  "Path":  "/",  "Roles":  [  {  "Ref":  "Role"  }  ]  }  },  "Role":  {  "Type":  "AWS::IAM::Role",  "Properties":  {  "Path":  "/",  "AssumeRolePolicyDocument":  {  "Version":  "2012-10-17",  "Statement":  [  {  "Effect":  "Allow",  "Principal":  {  "Service":  [  "ec2.amazonaws.com"  ]  },  "Action":  [  "sts:AssumeRole"  ]  }  ]  }  }  },  "EC2Instance":  {  "Type":  "AWS::EC2::Instance",  "Properties":  {  "IamInstanceProfile"  :  {  "Ref"  :  "InstanceProfile"  },  "InstanceType":  "t2.micro",  "SecurityGroups":  [  {  "Ref":  "InstanceSecurityGroup"  }  ],  "KeyName":  {  "Ref":  "KeyName"  },  "ImageId":  "ami-0cc293023f983ed53",  "Tags":  [  {  "Key":  "fu_stage",  "Value":  {  "Ref":  "AWS::StackName"  }  }  ],  "UserData":  {  "Fn::Base64":  {  "Fn::Join":  [  "\n",  [  "#!/bin/bash -v",  "sudo yum -y update",  "sudo yum -y install ruby wget",  "cd /home/ec2-user/",  "wget https://aws-codedeploy-eu-central-1.s3.amazonaws.com/latest/install",  "chmod +x ./install",  "sudo ./install auto",  "sudo service codedeploy-agent start"  ]  ]  }  }  }  },  "InstanceSecurityGroup":  {  "Type":  "AWS::EC2::SecurityGroup",  "Properties":  {  "GroupDescription":  "Enable SSH access via port 22",  "SecurityGroupIngress":  [  {  "IpProtocol":  "tcp",  "FromPort":  "22",  "ToPort":  "22",  "CidrIp":  {  "Ref":  "SSHLocation"  }  },  {  "IpProtocol":  "tcp",  "FromPort":  "80",  "ToPort":  "80",  "CidrIp":  "0.0.0.0/0"  }  ]  }  }  },  "Outputs":  {  "InstanceId":  {  "Description":  "InstanceId of the newly created EC2 instance",  "Value":  {  "Ref":  "EC2Instance"  }  },  "AZ":  {  "Description":  "Availability Zone of the newly created EC2 instance",  "Value":  {  "Fn::GetAtt":  [  "EC2Instance",  "AvailabilityZone"  ]  }  },  "PublicDNS":  {  "Description":  "Public DNSName of the newly created EC2 instance",  "Value":  {  "Fn::GetAtt":  [  "EC2Instance",  "PublicDnsName"  ]  }  },  "PublicIP":  {  "Description":  "Public IP address of the newly created EC2 instance",  "Value":  {  "Fn::GetAtt":  [  "EC2Instance",  "PublicIp"  ]  }  }  }  } 
```

我们需要在这里做很多事情。UserData 部分为我们设置了 CodeDeploy 守护进程，以便 AWS 可以将代码部署到这个实例。我们需要一个定义 SSH 的安全组来调试实例，并为应用程序打开 HTTP 访问。如果我们想定义一个自定义的参数，我们定义两个参数:keyname 和 SSH location。

注意标签`fu_stage`。这非常重要，因为这是 furnace 稍后用来查找要部署代码的实例的标签。

如我所说，为了找到正确的模板，Furnace 需要一点配置。您可以拥有任意多的模板，事情是这样的:

创建一个这样的文件夹结构:

```
.
├── stacks
│   ├── simple.template
│   └── mystack.yaml
└── .mystack.furnace 
```

其中`.mystack.furnace`包含这一行:

```
stacks/mystack.yaml 
```

这个 yaml 文件是 Furnace 的配置文件，可能包含如下内容:

```
main:
  stackname: MyStack
  spinner: 1
  plugins:
    plugin_path: "./plugins"
aws:
  code_deploy_role: CodeDeployServiceRole
  region: us-east-1
  template_name: simple.template
  app_name: stack-app
  code_deploy:
    # Only needed in case S3 is used for code deployment
    code_deploy_s3_bucket: furnace_code_bucket
    # The name of the zip file in case it's on a bucket
    code_deploy_s3_key: furnace_deploy_app
    # In case a Git Repository is used for the application, define these two settings
    git_account: Skarlso/furnace-codedeploy-app
    git_revision: b80ea5b9dfefcd21e27a3e0f149ec73519d5a6f1 
```

您可以在 README.md 中查看所有这些都做了什么，但现在重要的是，它创建的堆栈的名称，code_deploy 选项(使用 S3 或 Git 来查找要部署的代码)和 template_name(将用于查找模板)。

然后我们简单地说:

```
furnace-aws create mystack 
```

...它将继续创建堆栈并等待它完成。

如果一切正常，我们可以用`furnace-aws status mystack`查看堆栈，或者用`furnace-aws delete mystack`删除堆栈，或者通过`furnace-aws update mystack`更新堆栈。更新是通过变更集完成的，因此您将能够查看将要更新的内容。如果您定义了滚动更新，那么它将按照您定义的实例数进行滚动。

一旦您的基础设施完成，您就可以使用:
将您的代码部署到它上面

```
furnace-aws push 
```

这将使用所述设置来寻找代码并部署它。如果一切顺利，您应该能够通过 EC2 实例的公共 URL 访问您的应用程序(在这个简单的例子中)。如果您想推出一个新版本，只需再次运行相同的命令。

### GCP 应用部署

对于 GCP 来说，这是一样的，你不需要推送命令，因为 GCP 会负责应用程序的版本控制和部署。你的应用程序应该运行在 GCP 定义的 git 服务器上(或者运行在一个私有的 github 上，或者绑定到 github 上，这取决于你)。目录结构和配置选项几乎相同，但是对于 gcp 来说，it 熔炉的配置如下:

```
main:
  project_name: test-123
  spinner: 1
gcp:
  template_name: google_template.yaml
  stack_name: test2-stack 
```

简单多了。GCP 与金佳和 Yaml 和什么的照顾其余的。更复杂的例子请看这里:[熔炉 GCP](https://github.com/go-furnace/go-furnace/tree/master/templates) 。

基本上，它使用 startup_script.sh 来部署正确的代码，如下所示:

```
 - key: startup-script-url
            value: gs://{{ properties["bucket"] }}/startup-script.sh 
```

启动脚本位于这里: [startup_script.sh](https://github.com/go-furnace/go-furnace/blob/master/templates/startup-script.sh) 。这有点复杂，但是两个重要的部分是:

```
export HOME=/root
git config --global credential.helper gcloud.sh
git clone https://source.developers.google.com/p/$PROJECTID/r/<YOUR_REPO_HERE> /opt/app 
```

和

```
# Configure supervisor to start gunicorn inside of our virtualenv and run the
# application.
cat >/etc/supervisor/conf.d/python-app.conf << EOF [program:pythonapp]
directory=/opt/app/7-gce
command=/opt/app/7-gce/env/bin/gunicorn main:app --bind 0.0.0.0:8080
autostart=true
autorestart=true
user=pythonapp
# Environment variables ensure that the application runs inside of the
# configured virtualenv.
environment=VIRTUAL_ENV="/opt/app/env/7-gce",PATH="/opt/app/7-gce/env/bin",\ HOME="/home/pythonapp",USER="pythonapp"
stdout_logfile=syslog
stderr_logfile=syslog EOF supervisorctl reread
supervisorctl update 
```

它创建了一个将运行这个 python 应用程序的 supervisord 服务。这可以是任何持续运行的东西，比如 nginx，或者 caddy 等等。重点是这个脚本用于定义如何部署您的应用程序以及它位于何处。

要运行它，您只需调用:

```
furnace-gcp create teststack 
```

其中`.teststack.furnace`包含这单行:`stacks/gcp_furnace_config.yaml`。

而配置结构是这样的:

```
.
├── stacks
│   ├── gcp_furnace_config.yaml
│   ├── google_template.yaml
│   ├── simple_template.jinja
│   └── simple_template.jinja.schema
└── .teststac.furnace 
```

## 插件

Furnace 还提供了两个阶段的插件。堆栈创建前后。插件可以用任何支持 gRPC 的语言编写。这里有 Go 插件的例子:[熔炉 Go 插件](https://github.com/go-furnace/go-furnace/tree/master/furnace-aws/plugins/samples/plugins)和 Python 插件的例子:[熔炉 Python 插件](https://github.com/go-furnace/python-plugin)。

# 结论

哼。所以这可能看起来很多，但这里重要的一点是，熔炉并不妨碍你。你不会像使用 terraform 那样把自己锁在熔炉里。Furnace 使用 AWS 或 GCP 的现有部署管理服务。如果你决定不使用熔炉，你的配置和堆栈将仍然保留。你可以直接切换到 boto 或者 gcloud。Furnace 还为 AWS、GCP 和 DigitalOcean 提供了多个二进制文件。因此，与大约 110MB 的 terraform 相比，它真的很小。AWS 的熔炉大约为 16MB，GCP 大约为 16MB。

感谢您的阅读！
走，去看看[炉](https://github.com/go-furnace/go-furnace)。
婕婕莉。