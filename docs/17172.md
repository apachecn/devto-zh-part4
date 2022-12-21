# 使用 Spring 和 JWT 的安全性

> 原文：<https://dev.to/charles1303/security-using-spring-and-jwt-aai>

### 简介

我终于能够发表这篇文章了。花了这么长时间是因为两个主要原因；首先，在过去的几周里，我被一个非常有趣的项目吸引住了。当我们部署它，进行测试，并且看到所有的涉众对产品如何满足预期的标准和期望的特性感到满意时，感觉真的很好。其次，令人难过的是，我的硬盘崩溃了，恢复过程相当艰难。(我仍在评估迄今为止我丢失的东西，尤其是我的记录和文档)。但值得庆幸的是，我的大部分项目代码都在各自的 git 存储库中，增量与我笔记本电脑上的没有太大关系。这就是为什么我想利用这个机会再次提醒我们开发人员，我们应该总是尽可能频繁地将代码推送到当前的工作分支。即使任务没有完全完成，或者代码在工作一段时间后没有编译。不一定要每天都做，但至少要确保你在一周内做出承诺和努力。这种推动会让你在以后滔滔不绝。

既然我们已经解决了这个问题，让我们把注意力集中在我真正想在这篇文章中谈论的东西上。在我的上两篇文章[这里](https://dev.to/charles1303/a-use-case-implementation-for-external-api-integration-eoj)和[这里](https://dev.to/charles1303/exception-handling-and-logging-2okh)中，我展示了如何使用设计模式和开发应用程序的最佳实践来构建 REST APIs 并将它们与外部 API 集成。在本文中，我将演示如何使用 JWT 来实现安全性，以保护我们的 REST APIs。我还将强调保护我们的应用程序的其他策略，尤其是在数据方面。我知道这可能是一个熟悉的话题，但是它们通常是在脱离开发应用程序的整体环境下编写的。因此，我们将在我写的前几篇文章的基础上继续努力。在类、组件和方法引用方面，我仍然会使用相同的方法，所以你可能想在这里访问回购协议[，也可以看看以前的文章来了解一下。](https://github.com/charles1303/binlistImpl)

### 代码实现

假设你是一名顾问，你正准备和你的一个新客户预约。你知道训练是首先向客户注册为顾问，在那里你提供你的所有细节，这些细节将被保存在他们的记录中。这类似于当你第一次想使用一个应用程序时，我们在网上进行的注册过程。注册后，你就可以在约定的日期进行预约。到达现场后，你在接待处表明自己的身份，如果所有检查都通过，就会给你一个访客标签或卡片，允许你进入大楼的某些部分或楼层。当您注册时，可以使用您的凭证将这个过程映射到应用程序的登录过程。这是*认证*过程，如果您的凭证有效，您就可以访问应用程序。同时，您还会获得一个 JWT(令牌)(或者在有状态 web 应用程序中为您生成一个会话，或者为浏览器生成一个 cookie)。这个令牌代表发给你的访客标签。就像标记一样，token 将根据分配给 token 的角色授予您对应用程序域中资源的受控访问权限。这在*授权*过程中使用，以根据您的角色确定您是否可以访问某个资源(就像访问者的标签只授予您对大楼中某些楼层的访问权一样)。现在，就像只能在那一天使用的标签一样，令牌也有一个截止日期，并且变得无效，从而导致授权失败和拒绝访问资源。但是在某些情况下，假设您当天没有完成咨询，标签可以根据协议在第二天重复使用。同样，也可以根据请求刷新令牌，并在验证旧令牌后为其设置新的到期日期。如果你完成了咨询，你必须在离开大楼前提交标签。同样，当您使用完应用程序时，您需要注销，以便令牌失效。一旦您离开大楼或退出应用程序，您将不得不在接待处重复身份识别过程或再次登录过程，以访问设施或应用程序来执行您的活动。现在我们已经了解了身份验证、授权以及令牌生成和管理是如何协同工作的，让我们使用 Spring 安全框架和 JWT 将这些点联系起来:

首先，我们创建我们的 **JwtManager** 类来处理令牌信息的生成、验证和检索。然后我们创建我们的 **CustomUserDetails** 类。这个类将存储 Spring 认证用户或委托人的详细信息。通过实现*org . spring framework . security . core . user details . user details*接口，它可以访问用户名、密码以及通过用户分配的角色授予主体的权限等信息。接下来，我们创建我们的 **CustomUserDetailsService** 类。该类在它实现的*org . spring framework . security . core . user details . user details service*接口中定义了*loaduserbysusername*方法的自定义实现，并根据结果返回我们前面定义的 CustomUserDetails 实例。在这种情况下，我们使用提供的用户名查询数据库，看它是否存在。

然后我们创建我们的 **JwtAuthenticationFilter** 类。这个类过滤每个针对受保护资源的传入请求，以检查和验证使用 JwtManager 类中定义的 *validateToken* 方法发送的令牌。如果有效，它分别使用 JwtManager 类中定义的 *getUsernameFromJWT* 和 *getRolesFromJwt* 方法从令牌声明中提取用户名(主题)和角色，并使用它为用户创建有效的安全性上下文，以便用户在应用程序中保持身份验证。我们同样可以使用第二种方法，只使用 username 和 CustomUserDetailsService 类的 loadUserByUsername 方法，通过这段代码片段
来实现这一点

```
 String jwt = getJwtFromRequest(request);

            if (StringUtils.hasText(jwt) && jwtManager.validateToken(jwt)) {
                String username = jwtManager.getUsernameFromJWT(jwt);

        //Using an injected CustomUserDetailsService instance
        CustomUserDetails customUserDetails = customUserDetailsService.loadUserByUsername(username);
        UsernamePasswordAuthenticationToken authentication = new UsernamePasswordAuthenticationToken(customUserDetails.getPrincipal(), "", customUserDetails.getAuthorities());
                authentication.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));
                SecurityContextHolder.getContext().setAuthentication(authentication);
            }else {
                SecurityContextHolder.getContext().setAuthentication(null);
            } 
```

第二种方法的缺点是，对于每个请求，使用 loadUserByUsername 方法会有性能数据库命中，这与第一种方法不同，在第一种方法中，我们通过从发送的令牌中检索用户的详细信息来获得应用程序性能。另一方面，第二种方法允许在用户角色改变时实时修改授予的权限，而第一种方法要求令牌在生效前过期。无论您在这里采用哪种方法，通常都基于设计和需求决策。

接下来，我们将创建我们的 **CustomRestAccessDenied** 和**CustomRestAuthenticationEntryPoint**类。如你所见，它们分别实现了*org . spring framework . security . web . access . accessdeniedhandler*和*org . spring framework . security . web . authenticationentrypoint*接口。CustomRestAccessDenied 类中的 *handle* 方法旨在为用户已经过身份验证但没有访问资源的许可角色的请求触发，并返回 HTTP 403 状态，而 CustomRestAuthenticationEntryPoint 类中的*begin*方法为未经身份验证的用户触发，并返回 HTTP 401 状态。当我们查看我们的安全配置类实现时，您将看到这是如何设置的。还要注意响应是如何被定制为以特定格式返回的。这是为了在我们的应用程序中坚持一致的响应结构。对于那些看过我上一篇关于异常处理的文章的人来说，您可能想知道为什么我们没有在这个场景中使用这种方法。原因是拒绝访问和未授权的异常是在 Spring 的 ControllerAdvise 和 Exception 能够处理的外部级别抛出的，因此我们必须在它们发生的级别处理这些异常。

现在让我们看看我们的安全配置类，它是 **SecurityConfig** 类。扩展了*org . spring framework . security . config . annotation . web . configuration . websecurityconfigureradapter*的 SecurityConfig 类是我们迄今为止定义的所有以前的安全组件和类的粘合剂。它决定了它们在我们的应用安全实现中的行为和角色。 *securedEnabled、jsr250Enabled 和 prePostEnabled* 注释允许我们确定哪些角色应该能够访问特定的资源。这可以在您想要应用这些限制的方法上配置(例如在控制器中),或者将其附加到 *antMatchers* 中，正如在 SecurityConfig 类的 configure 方法中可以看到的。我们之前定义的 CustomUserDetailsService 类被注入，并被指定为被覆盖的*public void configure(AuthenticationManagerBuilder AuthenticationManagerBuilder)*方法中的身份验证组件。我们还配置了一个*password encoder*bean 来使用 Spring 的 *BCryptPasswordEncoder* 来验证用户的密码。在覆盖的*受保护的 void configure(http security http security)*方法中，我们为未授权和拒绝访问场景配置了异常处理程序，以分别使用注入的 CustomRestAuthenticationEntryPoint 和 CustomRestAccessDenied 类。我们禁用了 csrf，以便来自其他域的请求可以访问我们的端点，然后我们声明会话管理是无状态的，以便符合 REST 规范。然后，我们将注入的 JwtAuthenticationFilter 类配置为过滤器，对任何安全资源的每个请求都将调用该过滤器。最后，我们禁用/api/auth/ route 中端点的安全性，因为未经身份验证的用户会在注册和登录期间调用它。

现在让我们创建允许用户注册和登录我们的应用程序的组件。首先，我们创建我们的**用户**类实体，它将映射到数据库中的*用户*表。我们还创建了**角色**类实体，它将映射到*角色*表。然后，我们在角色表中插入以下角色:ROLE_USER 和 ROLE_ADMIN。然后，我们创建用于管理我们的用户实体的 **UserRepository** 和 **UserService** 类，以及用于管理我们的角色实体的 **RoleRepository** 和 **RoleService** 类。为了完整性，我们创建了一个 **RoleType** enum，它映射到我们刚刚在 roles 表中创建的角色。然后我们创建我们的 **AuthController** 类来处理注册和登录。如您所见，注册端点非常简单。我们创建一个具有指定角色的用户，并存储在数据库中。对于登录端点，您提供一个注册的用户名和密码，如果 Spring 安全框架的认证成功，则使用 Spring 的 *Authencation* 对象为用户设置一个有效的安全上下文。然后，JwtManager 的 *generateToken* 方法将认证作为一个参数，用用户名设置声明对象的主题，并将从主体获得的权限作为角色放入声明对象。然后使用构造的 Claims 对象生成一个令牌，并设置到期日期、发布时间，最后用我们的令牌密钥签名。

### 试运行

现在一切都设置好了，所以我们可以使用 mvn spring-boot:run 启动我们的 spring 应用程序，然后我们尝试使用我们的 **SignUpRequest** dto 类结构:
通过点击端点来注册

```
 POST    http://localhost:8080/api/auth/register
    {

    "name":"user1",
    "username":"username1",
    "email":"username1@email.com",
    "password":"password1"
    }

       Response:
    {
        "status": 0,
        "message": "User registered successfully",
        "result": null
    } 
```

对另一个用户重复上述操作来创建和管理

```
 POST    http://localhost:8080/api/auth/register/admin
    {

    "name":"user2",
    "username":"username2",
    "email":"username2@email.com",
    "password":"password2"
    }

        Response:
    {
        "status": 0,
        "message": "Admin registered successfully",
        "result": null
    } 
```

使用我们的 **LoginRequest** dto 类结构登录两个用户，并获得将为两个用户返回的各自的令牌

```
 POST http://localhost:8080/api/auth/login

    {
    "username":"username1",
    "password":"password1"
    }

        Response:
    {
        "status": 0,
        "message": "Token generated successfully",
        "result": {
        "accessToken": "eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJ1c2VybmFtZTEiLCJyb2xlcyI6WyJST0xFX0FETUlOIl0sImlhdCI6MTU2MDk4MDU1MywiZXhwIjoxNTYwOTgyMzUzfQ.boqHQh3gLPgNWBP0GiATBGg-25bwMfg33-zn5BFK9AiFuhYcWcmSSFp_isjlhL_xJ9WxMW6A7UWaVOXMdx94ng"
        }
    } 
```

在没有令牌或者令牌无效或过期的情况下调用我们的任何端点，将会得到一个由我们的 CustomRestAuthenticationEntryPoint 处理程序触发的响应，其状态代码为 HTTP 401

```
 {
        "timestamp": "2019-06-19T22:51:37.189",
        "message": "Unauthorized user",
        "status": 401
    } 
```

当调用具有未授权角色的端点时，比如说

`http://localhost:8080/card-scheme/stats?start=1&limit=10`

使用 user1 生成的令牌将给出一个由 CustomRestAccessDenied 处理程序触发的响应，状态代码为 HTTP 403，因为只有管理员角色可以访问它。

```
 {
        "timestamp": "2019-06-19T22:51:57.213",
        "message": "Access Denied",
        "status": 403
    } 
```

### 单元测试

现在让我们看看我们将如何设置编写我们的测试。因为我们关心身份验证和授权，我们将限制我们的测试范围不超出控制器层，因为这是这些限制的地方。为了实现这一点，我创建了 **SecurityTest** 类，并用 *@WebMvcTest* 注释对其进行了注释。然后，我模拟了控制器层需要的所有依赖项，除了我发现的 JwtManager。原因是，在我们的令牌生成和验证测试中，我将实际调用 JwtManager 类中的真实方法。然后我创建了 **AppTestConfig** 配置类，并在控制器层设置了基础包扫描。然而，我必须创建一个*entitymanager factory*bean，它将使用内存中的 H2 数据库进行启动和引导。在我的设置方法中，我添加了 JwtAuthenticationFilter 实例作为验证令牌的过滤器。最后我用*启用了 Spring security。apply(springSecurity())* 。运行测试将会给我们测试类中定义的预期结果。

在结束本文之前，让我们讨论一下保护应用程序的其他方面。

### 数据加密

自从人类能够交流以来，数据加密就一直存在。我不会深入这些证据的历史记录，但数据加密几乎与个人或团体之间的信息交换一样重要。数据加密是一种简单的行为，它使信息对除了预期接收者之外的非预期接收者变得无用或神秘。

在当今世界，数据加密基于两种不同形式的加密技术:

1.  对称密钥
2.  非对称密钥。

使用 *AES* 算法的对称密钥形式使用一个密钥进行加密和解密，并由相关方共享。另一方面，使用 *RSA* 算法的非对称密钥涉及两个密钥，公钥和私钥。公钥可以与任何人共享，但私钥必须保密。两者都可以用来加密消息，然后使用与最初用来加密消息的密钥相反的密钥来解码消息。还有对加密数据进行签名的概念，这对于*不可否认性*非常有用。这意味着使用私钥对加密的内容进行签名，然后使用公钥来验证签名。

尽管在我们的应用程序中确实没有实现这些加密形式的用例，但是您可以看看我创建的 **RSAEncryption** 、 **AESEncryption** 和**encryption andsignetest**类，以了解可用的实用程序方法以及它们是如何用于执行各种操作的。请注意，在 RSAEncryption 类中，您可以通过调用 *generateKeyPair* 方法来动态生成密钥，或者通过调用*getkeypairfromskeystore*方法从密钥库文件加载密钥。可以使用 Java keytool 实用程序生成密钥库文件。确定使用哪种形式(RSA 或 AES)的一个指导原则是要加密的数据的大小。对于大量数据，建议使用 AES 形式，因为它速度更快，计算量更小，而 RSA 可用于少量数据。

数据加密可以在静止和运动时进行。

运动数据加密的一个常见例子是使用 HTTPS 协议。这里发生的情况是，当浏览器向 SSL 服务器发出请求时，它获取公钥并生成一个随机的密钥。然后，它使用公钥加密生成的密钥，并将其发送回服务器。然后，服务器使用私钥解密并检索生成的密钥。当交换信息时，服务器和浏览器都使用这个生成的密钥进行加密和解密。这样做的好处是，只有浏览器会话和服务器知道用于信息交换的生成密钥。

对于非浏览器应用程序，我们也可以模拟上述场景，从而生成密钥对(使用我刚才提到的加密类的公钥和私钥)。然后，所涉及的应用程序交换它们的公钥，并用于加密和验证签名，而私钥用于解密和签名数据。

因此，在我们的案例中，我们同样可以通过利用我们在 HTTPS 协议上的应用来实现移动数据的数据加密。因此，我们的解决方案更多的是架构实现，而不是编码实现。

在静态数据加密的情况下，这通常涉及保护我们的数据存储组件或系统中的数据，如我们的数据库(无论是 RDBMS、NOSQL 还是我们的缓存系统)，甚至是我们的配置文件值。换句话说，使用我前面创建的加密实用程序类，我们可以以加密格式存储数据，并且只在应用程序中使用数据时才解密。如果数据落入不法之徒手中，这种方法会使我们的数据存储中的数据变得加密和不可读。我们也可以选择加密我们的数据，而不期望解密它，只要我们知道我们在加密它时使用的算法。这比加密安全得多，它被称为*散列*。

这里哈希的一个经典例子是在我们的数据库中存储用户密码(最近有人强烈抗议脸书以明文形式存储 Instagram 用户的密码)。我们在这里使用单向的散列实现来实现它。换句话说，与前面讨论的加密技术不同，它在被散列后无法被解密(或者实际上不可能)。这里的原则是，用特定的算法散列特定的字符串序列将总是产生相同的加密字符串(除了我将很快讨论的 Salting 的情况)。在这里，我们使用 BCryptPasswordEncoder*encode*方法来实现这一点，这在我们的 SecurityConfig 类中可以看到，它被用作我们的 PasswordEncoder，并在注册过程中创建我们的用户时使用。

仅仅散列我们的密码并不足以保护它们。彩虹表的可用性已经证明了这一点。这些工具的在线可用性使破解密码成为黑客的爱好，特别是非常简单和常见的密码，如流行的单词、连续的字母和数字，甚至个人信息，如姓名、日期等。这导致了被称为*盐渍*的概念。Salting 是一种添加随机字符序列作为密码哈希所需参数的一部分的技术。这使得使用 rainbow 表变得非常困难，因为由于 salting，即使上面提到的简单密码实例在这些表中也不再有条目。

在旧的实现中，Spring 提供了 MessageDigestPasswordEncoder 类，允许我们提供自己的 salt 字符串来散列我们的密码。一个有效的方法是为每个要散列的密码字符串提供一个不同的 salt 字符串(例如注册时以毫秒为单位的时间)。然而，这已经被弃用，取而代之的是 BCryptPasswordEncoder，我们目前在我们的应用程序中使用它。BCryptPasswordEncoder 不仅散列我们的密码，而且在这个过程中在内部为我们生成一个随机的 salt 字符串，从它的内部实现可以看出这一点。这种实现减轻了我们管理盐串生成的负担，因为盐串对我们密码的完整性至关重要。因此，每次散列相同的密码字符串时，都会产生不同的散列字符串。现在问题来了，我们如何确定比较和匹配密码？BCryptPasswordEncoder 实现将哈希字符串分解为 salt 字符串、成本参数和哈希值，所有这些都可以从哈希字符串中获得，并且由于 salt 确定了生成的哈希，因此可以使用它来确定提供的密码是否与我们存储的密码匹配。

最后，我想谈谈如何保护我们的应用程序配置文件，在我们的例子中，这个文件通常被命名为 **application.properties** 或 **application.yaml** 。虽然在我们的例子中，您会注意到像数据库凭证这样的敏感信息目前是从环境变量中检索的，但我也见过这些细节以纯文本形式存储在文件中的情况。尽管环境变量的使用在一定程度上降低了文件中以纯文本形式存在的风险，但另一种方法是使用 *Jasypt Spring boot starter 模块*。这个模块允许我们以加密的形式存储值，然后在运行时解密。

为了在我们的应用程序中实现这一点，我们首先需要将*jas ypt-spring-boot-starter*添加到我们的 **pom.xml**
中

```
<dependency>
    <groupId>com.github.ulisesbocchio</groupId>
    <artifactId>jasypt-spring-boot-starter</artifactId>
    <version>2.1.1</version>
</dependency> 
```

然后我们运行下面的命令

```
java -cp $M2_REPO/org/jasypt/jasypt/1.9.2/jasypt-1.9.2.jar  org.jasypt.intf.cli.JasyptPBEStringEncryptionCLI input="yourpassword" password=jasypt_password algorithm=PBEWithMD5AndDES 
```

在哪里

*   *$M2 回购*是你的 maven 回购目录
*   *输入*参数是你需要加密的密码，比如说你的数据库密码
*   *password* 参数是您将在启动应用程序时提供的用于加密的 Jasypt 密码或密钥
*   *算法*是由 Jasypt ( *PBEWITHMD5ANDDES，PBEWITHMD5ANDTRIPLEDES，PBEWITHSHA1ANDDESEDE，pbewithsha 1 and C2 _ 40*)提供的基于密码的加密(PBE)算法

上述命令将生成您提供的输入参数密码值的加密值。您可以对 application.properties 或 yaml 文件中想要加密的任何其他值重复该命令。

然后你用这些加密值设置属性或环境变量值，格式如下 *ENC(encrypted_value)* 例如在我们的 yaml 中，你可以像这样改变密码值

```
.
.
.
spring:
  profiles: development
  application:
    name: binList
  datasource:
     password: ENC(encrypted_password_from_jasypt_operation)
     .
     .
     . 
```

或者简单地保持 yaml 文件不变，并用它设置 SPRING_DATASOURCE_PASSWORD 环境变量

```
export SPRING_DATASOURCE_PASSWORD=ENC(encrypted_password_from_jasypt_operation) 
```

然后你要么像这样启动你的应用程序

```
mvn -Djasypt.encryptor.password=jasypt_password spring-boot:run 
```

或者您导出 JASYPT_ENCRYPTOR_PASSWORD 环境变量并简单地运行 mvn spring-boot:run

```
export JASYPT_ENCRYPTOR_PASSWORD=jasypt_password 
```

为了进一步加强应用服务器的安全性，您可以按顺序执行以下步骤

1.  使用批处理脚本导出并设置前面提到的所有环境变量。嘘还是。具体取决于您的操作系统)
2.  将应用程序作为后台服务启动，比如这个 mvn spring-boot:run &或者作为一个 Windows 服务
3.  使用批处理脚本取消设置所有环境变量，因为这些值只在应用程序启动时请求一次。
4.  只要您在别处有副本，就删除批处理脚本。

采取这些步骤的原因是为了防止用户使用 ps 和 history 命令查询您的应用服务器，以查看以前运行的命令中的密码或环境变量。

所以你有它。我们已经保护了我们的应用程序，虽然我们都知道安全永远不可能是完全的，但它应该会变慢，并需要更多的努力来防止有人恶意使用我们的应用程序。由于我们的应用程序中缺少 SQL 或 JQL 实现，我在这里还忽略了其他安全策略，特别是在数据访问对象层，例如 SQL 注入的查询输入参数化。您可以在这里访问本文[的更新源代码。](https://github.com/charles1303/binlistImpl)

在我的下一篇文章中，如果时间允许，我将展示如何使用 *Docker* 和一些代码重构来实现我们的应用程序的*可伸缩性*和*可用性*。我也希望从我的笔记本电脑崩溃中恢复到目前为止我们所做的 *Laravel 和 Node(NestJs)* 实现并分享它。那都是乡亲们！快乐编码。