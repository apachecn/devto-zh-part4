# RDS 和步入痛苦的中心

> 原文：<https://dev.to/jeffisadams/rds-and-stepping-into-the-plumbing-center-of-pain-gj3>

# RDS 和步入痛苦的管道中心:

AWS 托管服务基础设施非常方便，这是我以前没有意识到的。假设你想要一个 lambda，把它推送到某种 SQS 队列。您添加了一个策略...就是这样。它们根据您的配置管理队列中的数据。如果您想写入 DynamoDB，情况类似。只是一个政策。有弊端。当您的数据实际存储在 DynamoDB 表中时，这变得有点不透明。但是您可以通过添加策略从 lambda 连接到它。因此，当我决定创建一个由 SQL 数据库支持的 lambda 而不是 DynamoDB 时，我期望有类似的便利性，但却进入了痛苦的中心(VPC)。

[![Fancy plumbing center](img/eb94a331dad7b9e1eac88b4ca360f76c.png "Plumbing")](https://res.cloudinary.com/practicaldev/image/fetch/s--ln7gG1WI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://tenmilesquare.com/wp-content/uploads/2019/07/antique-bathroom-bathroom-sink-faucet-161417-1024x683.jpg)

## 以 RDS 支持的 VPC 为例

特别感谢 Jason Mao 帮助审查网络 VPC 设置。

## TLDR；

代码位于[这里](https://github.com/jeffisadams/RDS-lambda)。

## 先决条件

*   [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)
*   [AWS 无服务器应用模型 CLI](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-install.html)
*   [出发](https://golang.org/doc/install)
*   [命令行访问您的环境](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html)

## 运行

*   设置以下环境变量
    *   堆栈名称
    *   STACK_BUCKET(这个 BUCKET 必须存在于您的 AWS 环境中)
    *   YOUR_IP 什么是您的公共 IP，以便将其添加到安全组
    *   这是您能够访问数据库的唯一方式
*   连续运行以下程序:
    *   `make build`
    *   `make deploy_rds`
    *   `make init_db`
*   运行`curl {{Url from the output tab of your cloudformation template}}`
    *   您应该看到从数据库中检索并序列化了两条记录
    *   [bob@example.com](mailto:bob@example.com)
    *   [jane@example.com](mailto:jane@example.com)

## RDS

RDS 宣传的愿景是一个易于设置、操作和扩展的关系数据库。“它在许多方面实现了这个目标，但有一个非常重要的区别。 **RDS 在 EC2** 上运行。您需要为计算付费，更重要的是，您必须设置底层网络。大多数教程只是将您的数据库附加到您的默认 VPC。这并不是一件坏事，但是如果在您的[基础设施中没有可引用的 VPC 作为代码](https://en.wikipedia.org/wiki/Infrastructure_as_code)，那么通过[云形成](https://aws.amazon.com/cloudformation/)或 [SAM](https://aws.amazon.com/serverless/sam/) 添加安全组就变得非常困难。

所以现在我掉进了建立 VPC 的兔子洞。我想用 Cloudformation / SAM 来做。有很多关于 VPC 是什么以及如何设置它的很好的入门书籍(见链接)，但是我发现很少有人把基础设施作为代码，我知道为什么。很复杂。因此，你要么通过浏览器获得 Hello World VPC 教程，要么获得只是模板和悲伤的大师课。

本教程的目标是展示尽可能简单的端到端 RDS 加 Lambda 加 VPC。我的重点是 Lambda 和 Lambda 到 RDS 网络连接的细节。如果你要在生产中运行这样的东西，你将需要关于 VPC 的更深入的指导。我会在底部附上一些我喜欢的教程。我还使用用户名和密码连接到 lambda。AWS 建议使用角色和身份验证令牌进行连接。正如 Git 历史将揭示的那样，我开始走这条路，但是发现范围越来越大，我的精神健康崩溃了。我将在最后添加一些认证令牌教程。

# 代码

## 无服务器应用模型(SAM)模板

我总是建议利用某种基础设施作为代码。这东西很复杂，你需要迭代。我向你保证，如果你用浏览器安装了 VPC，下次你需要它的时候，你会忘记所有的步骤。有一个参考和一些步骤是至关重要的。

### VPC

这个模板很长，有很多活动的部分。他们几乎都献给了 VPC。我的目标是获得 lambda 连接，所以我在这里忽略了大量的细节。同样重要的是要记住，AWS 并没有发明这些概念。他们只是以令人困惑的方式更改了名称，这让营销部门很高兴。记住这一点，如果你已经掌握了一些其他的网络知识，这将会容易得多。确保您了解分层模型，并且最好了解 IP 和 CIDR 如何路由流量。这是我们正在构建的 AWS 资源。对于其他用例，还有更多的与 VPC 相关的东西需要构建，但是我故意忽略它们，以使我们专注于这个用例。

*   VPC
    *   其他事物的抽象组合。与 AWS 其余部分逻辑隔离的网络。
*   子网络
    *   为简单起见，这两个子网都是公共子网。
    *   我们正在建立两个，因为我们需要两个进入 RDS 组，这就是告诉 RDS 在云形成中使用什么 VPC。
*   互联网网关
    *   用于通过互联网访问您的 VPC 中的资源。主要是允许流量流出，但不允许流量流入。
*   路由表
    *   基于 IP 的路由表。如果每个子网需要不同的规则，可以有多个。请注意将流量发送到互联网网关的路由规则。基本上，如果一个 IP 地址出现，而我们不知道它连接到哪里。前往公开互联网寻找答案。

```
VPC:
  Type: AWS::EC2::VPC
  Properties:
    CidrBlock: 10.1.0.0/16
    EnableDnsSupport: true
    EnableDnsHostnames: true
    Tags:
    - Key: name
      Value:  !Join ['', [!Ref DatabaseName, "-VPC" ]]   
InternetGateway:
  Type: AWS::EC2::InternetGateway
  DependsOn: VPC
AttachGateway:
  Type: AWS::EC2::VPCGatewayAttachment
  Properties:
    VpcId: !Ref VPC
    InternetGatewayId: !Ref InternetGateway

PublicSubnetA:
  Type: AWS::EC2::Subnet
  Properties:
    VpcId: !Ref VPC
    CidrBlock: 10.1.10.0/24
    AvailabilityZone: !Select [ 0, !GetAZs ]
    Tags:
    - Key: Names
      Value: !Sub ${DatabaseName}-PublicA
PublicSubnetB:
  Type: AWS::EC2::Subnet
  Properties:
    VpcId: !Ref VPC
    CidrBlock: 10.1.20.0/24
    AvailabilityZone: !Select [ 1, !GetAZs ]
    Tags:
    - Key: Names
      Value: !Sub ${DatabaseName}-PublicA

PublicRouteTable:
  Type: AWS::EC2::RouteTable
  Properties:
    VpcId: !Ref VPC
PublicRoute:
  Type: AWS::EC2::Route
  DependsOn: AttachGateway
  Properties:
    RouteTableId: !Ref PublicRouteTable
    DestinationCidrBlock: 0.0.0.0/0
    GatewayId: !Ref InternetGateway

PublicSubnetARouteTableAssociation:
  Type: AWS::EC2::SubnetRouteTableAssociation
  Properties:
    SubnetId: !Ref PublicSubnetA
    RouteTableId: !Ref PublicRouteTable

PublicSubnetBRouteTableAssociation:
  Type: AWS::EC2::SubnetRouteTableAssociation
  Properties:
    SubnetId: !Ref PublicSubnetB
    RouteTableId: !Ref PublicRouteTable 
```

Enter fullscreen mode Exit fullscreen mode

这里的目标是让我们在 VPC 中构建可以相互访问的 lambda 和 RDS 的绝对最小值。还有许多其他组件可用于其他用例。值得注意的是，该 VPC 全部是公共子网，因此不适合高安全性环境。

### 安全组管道连接到 Lambda

安全组添加了第二层网络规则，我们可以覆盖这些规则来确定哪些机器/功能可以通信。我们创建数据库安全组以允许所有传出流量、来自您家庭 IP 地址的传入流量以及来自 Lambda 安全组的传入流量。这是允许 lambda 进程在不知道 Lambda 容器的 IP 地址的情况下访问 RDS 数据库的步骤。

```
LambdaSecurityGroup:
  Type: AWS::EC2::SecurityGroup
  Properties:
    GroupDescription: Allow http to client host
    VpcId: !Ref VPC
    SecurityGroupEgress:
    - IpProtocol: '-1'
      FromPort: -1
      ToPort: -1
      CidrIp: 0.0.0.0/0

DatabaseSecurityGroup:
  Type: AWS::EC2::SecurityGroup
  DependsOn:
    - PublicSubnetA
    - PublicSubnetB
  Properties:
    GroupDescription: Allow http to client host
    VpcId: !Ref VPC
    SecurityGroupIngress:
    # Allows you to log into mysql from home
    - IpProtocol: tcp
      FromPort: 3306
      ToPort: 3306
      CidrIp: !Sub ${YourExternalIp}/32
    # Rule to allow traffic in from resources that are attached to the Lambda Security group
    # This is the specific rule that allows the Lambda to have network access to our RDS cluster
    - IpProtocol: tcp
      FromPort: 3306
      ToPort: 3306
      SourceSecurityGroupId: !GetAtt LambdaSecurityGroup.GroupId
    SecurityGroupEgress:
    - IpProtocol: "-1"
      FromPort: -1
      ToPort: -1
      CidrIp: 0.0.0.0/0 
```

Enter fullscreen mode Exit fullscreen mode

### 好的唷...RDS 时间

相比之下，这非常简单。为样板版本添加三个资源。

*   抽象集群
*   一个例子
*   子网组我们使用子网组选择 VPC，否则它只连接到默认的 VPC。默认的 VPC 适用于很多情况，但是使得模板创建安全组变得困难。这里是没有 VPC 的零集群和 api 崩溃的地方。该实例将与 EC2 实例一一对应，并通过两个配置设置应用我们在上面花了很多时间的网络规则。显然，添加更多的实例是可能的，我们需要冗余来生产，但这只是一个开始。

注意:如果您在生产中使用 SSM 参数来存储您的用户名/密码，请使用这些参数。我再次希望事情尽可能简单，但这是在生产的实际实现中需要更高安全性的另一个地方。(SSM 教程)[[https://AWS . Amazon . com/blogs/mt/integrating-AWS-cloud formation-with-AWS-systems-manager-parameter-store/](https://aws.amazon.com/blogs/mt/integrating-aws-cloudformation-with-aws-systems-manager-parameter-store/)]

```
RDSDatabase:
  Type: AWS::RDS::DBCluster
  Properties:
    DBClusterIdentifier: !Ref DatabaseName
    Engine: aurora
    EnableIAMDatabaseAuthentication: true
    MasterUsername: !Ref AdminUserName
    MasterUserPassword: !Ref AdminUserPassword
    DBSubnetGroupName: !Ref PublicSubnetGroup
    VpcSecurityGroupIds:
      - !GetAtt DatabaseSecurityGroup.GroupId
RDSInstance1:
  Type: AWS::RDS::DBInstance
  DependsOn:
    - PublicSubnetA
  Properties:
    DBClusterIdentifier: !Ref RDSDatabase
    # Customize this to the size machine you want
    DBInstanceClass: db.r4.large
    Engine: aurora
    PubliclyAccessible: true
    DBSubnetGroupName: !Ref PublicSubnetGroup 
```

Enter fullscreen mode Exit fullscreen mode

知道您的 RDS 集群可通过互联网路由。通过正确的安全组设置，您可以直接访问该数据库。这对于调试和数据清理很有帮助，但是根据里面的数据和您的安全配置文件，这可能是有风险的。可以创建私有子网，使 lambda 能够访问数据库集群，而不从外部路由数据库集群。

### API 网关

这是 API 网关中的一个简单的 lambda，它只是从数据库中检索数据。我们使用环境传递配置。一般来说，我们希望使用令牌认证，但这篇文章已经太长了，所以我在下面链接了如何添加令牌认证的教程。

```
ServiceApi:
  Type: AWS::Serverless::Api
  Properties:
    Name: ServiceApi
    StageName: !Ref Version
LambdaFunction:
  Type: AWS::Serverless::Function
  Description: Function to get the data out of the database
  Properties:
    Handler: ./dist/main
    Timeout: 30
    Policies:
      - AWSLambdaVPCAccessExecutionRole
    Environment:
      Variables:
        DB_SERVICE_USER: !Ref ServiceUserName
        DB_SERVICE_PASSWORD: !Ref ServiceUserPassword
        DB_HOST: !GetAtt RDSDatabase.ReadEndpoint.Address
        DB_NAME: !Ref DatabaseName
        DB_TABLE_NAME: !Ref DatabaseTableName
    VpcConfig:
      SecurityGroupIds:
        - !GetAtt LambdaSecurityGroup.GroupId
      SubnetIds:
        - !Ref PublicSubnetA
        - !Ref PublicSubnetB
    Events:
      CreateUser:
        Type: Api
        Properties:
          Path: /transactions
          RestApiId: !Ref ServiceApi
          Method: Get 
```

Enter fullscreen mode Exit fullscreen mode

关键是 lambda 与 DB 运行在同一个 VPC 中，允许我们通过安全组规则访问它。由于我们的数据库在技术上也是公开的，我们可以从外部设置访问，但是这样我们就不能使用安全组来保护数据库。根据定义，Lambda 没有一致的 IP 地址，因此我们无法应用基于 IP 的规则。相反，我们将其添加到 VPC 的安全组中。

# 运行时的胆量(但先点 init)

我们现在已经设置好了所有的 AWS 资源。但是，API 端点将失败，因为它依赖于一个尚不存在的用户。

```
Environment:
  Variables:
    DB_SERVICE_USER: !Ref ServiceUserName
    DB_SERVICE_PASSWORD: !Ref ServiceUserPassword
    DB_HOST: !GetAtt RDSDatabase.ReadEndpoint.Address
    DB_NAME: !Ref DatabaseName
    DB_TABLE_NAME: !Ref DatabaseTableName 
```

Enter fullscreen mode Exit fullscreen mode

所以我创建了一个脚本来创建一个用户。这是通常具有令牌认证的部分，但是让我们关注连接性，我将让更好的教程展示令牌认证步骤(见下文)。

### 初始化和用户创建

我们需要一个用户、一个数据库、一个表和一些数据。所以让我们挥挥手，运行脚本`make init_db`。这会在 Go 中使用命令行环境变量运行一个脚本。我最初的意图是创建一个自定义资源来做这件事，但是发现自定义资源的 Go 库令人恼火。如果你有任何类型的错误，Cloudformation 将等待 60 分钟，看看情况是否稳定。此外，由于 Lambda 在 VPC 中运行。拆除这个云层模板也需要大约 30 分钟。我在这个问题上浪费了很多时间。第二，Lambda 会有环境变量和你的主用户名和密码，这似乎不是一个好主意。

```
db.MustExec(fmt.Sprintf(`CREATE DATABASE IF NOT EXISTS %s`, dbName))
db.MustExec(fmt.Sprintf(`USE %s`, dbName))

db.MustExec(fmt.Sprintf("CREATE USER '%s'@'%%' IDENTIFIED BY '%s';", dbServiceUser, dbServicePassword))
db.MustExec(fmt.Sprintf("GRANT ALL ON %s.* TO '%s'@'%%';", dbName, dbServiceUser))
// db.MustExec("FLUSH PRIVILEGES;")

db.MustExec(fmt.Sprintf(`
CREATE TABLE IF NOT EXISTS %s(
  id INT NOT NULL AUTO_INCREMENT,
  email varchar(255) DEFAULT NULL,
  date TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (id),
  CONSTRAINT UNIQUE(email)
);`, dbTableName))

db.MustExec(fmt.Sprintf(`INSERT INTO %s (email) VALUES ("bob@example.com");`, dbTableName))
db.MustExec(fmt.Sprintf(`INSERT INTO %s (email) VALUES ("jane@example.com");`, dbTableName)) 
```

Enter fullscreen mode Exit fullscreen mode

### 实际λ

这很简单，但是很大程度上依赖于之前的所有内容。

```
func handleCrudRequest() (events.APIGatewayProxyResponse, error) {
    dbTableName := os.Getenv("DB_TABLE_NAME")
    transactions := []UserTransaction{}
    err := db.Select(&transactions, fmt.Sprintf(`SELECT * from %s`, dbTableName))
    if err != nil {
        fmt.Println(err)
    }

    out, serializationErr := json.Marshal(transactions)
    if serializationErr != nil {
        fmt.Println(serializationErr)
    }

    return events.APIGatewayProxyResponse{
        StatusCode: 200,
        Body:       string(out),
    }, nil
}

func main() {
    lambda.Start(handleCrudRequest)
}

func init() {
    // Login to the DB
    dbHost := os.Getenv("DB_HOST")
    dbUser := os.Getenv("DB_SERVICE_USER")
    dbPassword := os.Getenv("DB_SERVICE_PASSWORD")
    dbName := os.Getenv("DB_NAME")

    connectionStr := fmt.Sprintf("%s:%s@tcp(%s:%s)/%s?timeout=10s", dbUser, dbPassword, dbHost, "3306", dbName)
    var err error
    db, err = sqlx.Connect("mysql", connectionStr)
    if err != nil {
        log.Fatalln(err)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们使用环境变量来传递数据库连接的表和用户设置。然后从我们的桌子上拿走并归还。在成功运行之前，请确保您运行了`make init_db`

# 这个怎么考

在 Cloudformation 中，在您的模板中查找 output 选项卡，并跟随该链接(结构与下面类似，但带有您的 id)。您应该看到返回的两个对象的 json 列表:

运行`curl https://xxxxxxxxxx.execute-api.us-xxxxxxxx.amazonaws.com/v1/transactions`

```
[  {  id:  1,  email:  "bob@example.com",  date:  "2019-07-12 19:09:25"  },  {  id:  2,  email:  "jane@example.com",  date:  "2019-07-12 19:09:25"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

# 总结我们建造了什么

*   一个正常工作的 VPC，在两个可用性区域中有两个公共子网。
*   我们的 VPC 中的管道可以访问 RDS 数据库
    *   允许不使用 IP 地址的流量的安全组
*   一个 RDS 集群，其中一个实例在一个可用性区域中运行(我们创建了两个区域，因为您必须有两个区域来创建 RDS 的子网组)。
*   在本地运行的数据库初始化脚本，用于创建:
    *   一个数据库
    *   我们可以用来登录的用户
    *   一张桌子
    *   我们可以提取的数据
*   一个 API Gateway Lambda 函数，它对在同一个 VPC 中运行的数据库运行一个简单的查询，并将其输出

## 别忘了拆掉这一个上的基础设施

因为这个模板运行 EC2 机器，所以价格比 DynamoDB 或 Queues 或其他完全托管的服务增加得快得多。运行`make teardown`进行清理。

## 阅读清单

*   常规链接
    *   [虚拟私有云(VPC)](https://aws.amazon.com/vpc/)
*   VPC 教程
    *   [VPC 路由](https://medium.com/@mda590/aws-routing-101-67879d23014d)
*   AWS RDS 令牌身份验证连接教程
    *   [令牌认证](https://aws.amazon.com/premiumsupport/knowledge-center/users-connect-rds-iam/)
    *   [AWS 令牌认证示例](https://github.com/aws/aws-sdk-go/blob/master/example/service/rds/rdsutils/authentication/iam_authentication.go)
    *   [在 GO 中有工作 RDSUtils 代码的那个](https://luktom.net/en/e1544-aws-lambda-and-mysql-iam-authentication-in-go)
    *   [一个正在进行的问题](https://stackoverflow.com/questions/48138267/unable-to-access-rds-database-via-iam-authentication)