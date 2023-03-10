# 在 Spring Boot 应用程序中嵌入 Bonita 引擎

> 原文：<https://dev.to/bonitasoft/embed-bonita-engine-in-a-spring-boot-application-4377>

本教程是一个如何将 Bonita 引擎(BPM 工作流引擎)嵌入到 **Spring Boot** 应用程序中的例子。

提出的用例是一个基于流程的应用程序，允许某人向银行申请贷款。银行将审查、批准或拒绝这一请求，并对这一决定做出解释。

## 范围

在本教程中，您将学习如何使用 Spring Boot 框架编写一个应用程序，集成 Bonita 执行引擎来运行流程。您将学习如何配置 Bonita 引擎来指向您选择的数据库并调优连接池。
您将学习如何以编程方式构建流程，部署和执行它们。
您还将学习如何部署用 [Bonita Studio](https://documentation.bonitasoft.com/bonita/current/bonita-bpm-overview) 生成的流程。

### TLDR；

本教程的完整源代码可以直接访问，有两种不同的风格:

*   第一个例子展示了如何用 [Gradle](https://gradle.org/) 构建应用程序。
*   第二个例子展示了如何用 [Maven](https://maven.apache.org/) 构建应用程序。在 Gradle 示例中提供了[完整教程细节](https://github.com/bonitasoft/bonita-examples/tree/master/bonita-loan-request-application/loan-request-app-gradle-kotlin)，但是在 Maven 示例中一切都是一样的。只有构建部分是特定的。

## 先决条件

### 数据库

如果您只想运行一个嵌入式 H2 数据库，什么都不需要。

要让您的应用程序指向 MySQL、PostgreSQL、Microsoft SQL Server 或 Oracle 数据库，请确保您有一个启动并运行的数据库服务器，并且它包含一个专用于 Bonita 引擎的模式(默认名称是`bonita`)。

关于 Bonita 的数据库准备的更深入的细节，参见[具体文档页面](https://documentation.bonitasoft.com/bonita/current/database-configuration)。

然后阅读下面如何[配置数据库访问](#configure-the-database)。

### 流程

本教程假设您具备 BPMN /过程设计的基本知识。

## 用例

对于这个例子，我们将开发以下流程并与之交互。

[![Loan Request process diagram](img/7a27f0f9173ad18657e2a73438d48073.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--52tctw54--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jixct237pxziilvnadvt.png)

## 让我们一步一步地编写应用程序

在这个例子中，我们建议使用 **Gradle** 作为构建工具，使用 **Kotlin** 作为编程语言。
如果你不熟悉 Gradle，有一个 [Maven 版本的这个例子](https://github.com/bonitasoft/bonita-examples/tree/master/bonita-loan-request-application/loan-request-app-maven-kotlin)。
如果你不熟悉 Kotlin，不要担心，如果你懂 Java 或类似的语言，它可以很容易地阅读。

### 应用程序的自举，使用 Spring Boot

让我们用最低的 Spring Boot +科特林需求:
来写一个 Gradle 构建(文件`build.gradle.kts`

```
import org.jetbrains.kotlin.gradle.tasks.KotlinCompile

plugins {
    id("org.springframework.boot") version "2.1.6.RELEASE"
    id("io.spring.dependency-management") version "1.0.7.RELEASE"
    kotlin("jvm") version "1.3.41"
    kotlin("plugin.spring") version "1.3.41"
}

repositories {
    mavenCentral()
    jcenter()
}

dependencies {
    // Embed an application server:
    implementation("org.springframework.boot:spring-boot-starter")

    // Libs to code in Kotlin:
    implementation("org.jetbrains.kotlin:kotlin-reflect")
    implementation("org.jetbrains.kotlin:kotlin-stdlib-jdk8")
}

java.sourceCompatibility = JavaVersion.VERSION_1_8

// configure Kotlin compiler:
tasks.withType<KotlinCompile> {
    kotlinOptions {
        freeCompilerArgs = listOf("-Xjsr305=strict")
        jvmTarget = "1.8"
    }
} 
```

编写主 Spring Boot 类来启动我们的应用程序:

```
package org.bonitasoft.loanrequest

import org.springframework.boot.autoconfigure.SpringBootApplication
import org.springframework.boot.runApplication

@SpringBootApplication
class LoanRequestApplication

fun main(args: Array<String>) {
    runApplication<LoanRequestApplication>(*args)
} 
```

在当前状态下，通过在命令行中键入:
，我们的应用程序已经可以运行(但不做任何事情)

```
./gradlew bootRun 
```

然后可以在 [http://localhost:8080/](http://localhost:8080/) 访问

### 添加 Bonita 引擎

下一步，让我们将 Bonita 引擎添加到我们的`build.gradle.kts` :
中

```
...
dependencies {
    ...
    val bonitaEngineVersion = "7.9.0"

    // adding dependency on bonita-engine-spring-boot-starter automatically provides
    // and starts a Bonita Engine when used in a Spring Boot application:
    implementation("org.bonitasoft.engine:bonita-engine-spring-boot-starter:$bonitaEngineVersion")

    // use bonita-client to be able to interact with the running Engine
    // to deploy and run instances of processes:
    implementation("org.bonitasoft.engine:bonita-client:$bonitaEngineVersion")

    // Add the database driver we want Bonita to use:
    runtime("com.h2database:h2:1.4.199")
    ...
} 
```

Spring Boot 的魔力发生了，当我们的应用程序启动时，Bonita 引擎会自动启动。
我们可以在控制台看到引擎启动日志:

```
|09:44:15.601|main|INFO |o.b.p.s.ScriptExecutor| configuration for Database vendor: h2
|09:44:15.989|main|INFO |o.b.p.s.PlatformSetup| Connected to 'h2' database with url: 'jdbc:h2:file:./build/h2_database/bonita' with user: 'BONITA'
|09:44:16.341|main|INFO |o.b.p.s.ScriptExecutor| Executed SQL script file:/home/manu/.gradle/caches/modules-2/files-2.1/org.bonitasoft.platform/platform-resources/7.9.0/c183cb/platform-resources-7.9.0.jar!/sql/h2/createTables.sql
...
|09:44:26.437|main|INFO |o.b.e.a.i.PlatformAPIImpl| THREAD_ID=1 | HOSTNAME=manu-laptop | Start service of platform : org.bonitasoft.engine.classloader.ClassLoaderServiceImpl
|09:44:26.438|main|INFO |o.b.e.a.i.PlatformAPIImpl| THREAD_ID=1 | HOSTNAME=manu-laptop | Start service of platform : org.bonitasoft.engine.cache.ehcache.PlatformEhCacheCacheService
|09:44:26.490|main|INFO |o.b.e.a.i.PlatformAPIImpl| THREAD_ID=1 | HOSTNAME=manu-laptop | Start service of platform : org.bonitasoft.engine.service.BonitaTaskExecutor
...
|09:44:26.708|main|INFO |o.b.e.a.i.t.SetServiceState| THREAD_ID=1 | HOSTNAME=manu-laptop | TENANT_ID=1 | start tenant-level service org.bonitasoft.engine.cache.ehcache.EhCacheCacheService on tenant with ID 1
|09:44:26.718|main|INFO |o.b.e.a.i.t.SetServiceState| THREAD_ID=1 | HOSTNAME=manu-laptop | TENANT_ID=1 | start tenant-level service org.bonitasoft.engine.business.data.impl.JPABusinessDataRepositoryImpl on tenant with ID 1 
```

### 让我们编写我们的第一个流程

我们的平台正在运行，我们现在可以创建一个 LoanRequestProcessBuilder 类来构建一个 Bonita 流程并返回一个 DesignProcessDefinition。让我们建立上图中设计的流程。

```
package org.bonitasoft.loanrequest.process

// imports removed for readability

const val ACTOR_REQUESTER = "Requester"
const val ACTOR_VALIDATOR = "Validator"

const val START_EVENT = "Start Request"
const val REVIEW_REQUEST_TASK = "Review Request"
const val DECISION_GATEWAY = "isAccepted"
const val SIGN_CONTRACT_TASK = "Sign contract"
const val NOTIFY_REJECTION_TASK = "Notify rejection"
const val ACCEPTED_END_EVENT = "Accepted"
const val REJECTED_END_EVENT = "Rejected"

const val CONTRACT_AMOUNT = "amount"

class LoanRequestProcessBuilder {

    fun buildExampleProcess(): DesignProcessDefinition {
        val processBuilder = ProcessDefinitionBuilder().createNewInstance("LoanRequest", "1.0")
        // Define the actors of the process:
        processBuilder.addActor(ACTOR_REQUESTER, true) // only requester can initiate a new process
        processBuilder.addActor(ACTOR_VALIDATOR) // only requester can initiate a new process
        // Define the tasks
        processBuilder.addUserTask(REVIEW_REQUEST_TASK, ACTOR_VALIDATOR)
        processBuilder.addUserTask(SIGN_CONTRACT_TASK, ACTOR_REQUESTER) // Imagine this task involve paper signing

        // For completion, this auto-task should have a connector on it,
        // to notify the rejection (through email connector, for example):
        processBuilder.addAutomaticTask(NOTIFY_REJECTION_TASK)

        // Define the events:
        processBuilder.addStartEvent(START_EVENT)
        processBuilder.addEndEvent(ACCEPTED_END_EVENT)
        processBuilder.addEndEvent(REJECTED_END_EVENT)
        // Define the Gateway:
        processBuilder.addGateway(DECISION_GATEWAY, GatewayType.EXCLUSIVE)
        // Define transitions:
        processBuilder.addTransition(START_EVENT, REVIEW_REQUEST_TASK)
        processBuilder.addTransition(REVIEW_REQUEST_TASK, DECISION_GATEWAY)
        processBuilder.addTransition(DECISION_GATEWAY, SIGN_CONTRACT_TASK,
                // let's simulate a human decision with a random accepted / rejected decision:
                ExpressionBuilder().createGroovyScriptExpression("random decision", "new java.util.Random(System.currentTimeMillis()).nextBoolean()", "java.lang.Boolean")
        )
        processBuilder.addDefaultTransition(DECISION_GATEWAY, NOTIFY_REJECTION_TASK) // Default transition, taken is expression above returns false
        processBuilder.addTransition(SIGN_CONTRACT_TASK, ACCEPTED_END_EVENT)
        processBuilder.addTransition(NOTIFY_REJECTION_TASK, REJECTED_END_EVENT)

        // Define a contract on the process initiation:
        processBuilder.addContract().addInput(CONTRACT_AMOUNT, Type.DECIMAL, "Amount of the loan requested")
        // Here we imagine a more complex contract with more inputs...

        return processBuilder.process
    }

} 
```

那么必须部署和启用该流程。让我们创建一个专用类:

```
package org.bonitasoft.loanrequest.process

// imports...

class ProcessDeployer {

    @Throws(BonitaException::class)
    fun deployAndEnableProcessWithActor(designProcessDefinition: DesignProcessDefinition,
                                        requesterActor: String,
                                        requesterUser: User,
                                        validatorActor: String,
                                        validatorUser: User): ProcessDefinition {
        // Create the Actor Mapping with our Users:
        val requester = Actor(requesterActor)
        requester.addUser(requesterUser.userName)
        val validator = Actor(validatorActor)
        validator.addUser(validatorUser.userName)
        val actorMapping = ActorMapping()
        actorMapping.addActor(requester)
        actorMapping.addActor(validator)

        // Create the Business Archive to deploy:
        val businessArchive = BusinessArchiveBuilder().createNewBusinessArchive()
                .setProcessDefinition(designProcessDefinition)
                // set the actor mapping so that the process is resolved and can then be enabled:
                .setActorMapping(actorMapping)
                .done()

        with(apiClient.processAPI) {
            val processDefinition = deploy(businessArchive)
            enableProcess(processDefinition.id)
            return processDefinition
        }
    }
} 
```

现在让我们从主应用程序调用我们的构建和部署方法:

```
val apiClient = APIClient()

// Log in on Engine API:
loginAsTenantAdministrator()
// Create business users to interact with the process:
val requester = createNewUser("requester", "bpm", "Requester", "LoanRequester")
val validator = createNewUser("validator", "bpm", "Validator", "LoanValidator")
// Use this newly created users to create and execute the process flow:
loginWithAnotherUser(requester)
val processDefinition = createAndDeployProcess(requester, validator)

// Utility function to log in to Bonita with the provided Administrator User:
private fun loginAsTenantAdministrator() {
    apiClient.logout()
    apiClient.login(TENANT_ADMIN_NAME, TENANT_ADMIN_PASSWORD)
}

// Utility function to create a User in Bonita:
private fun createNewUser(userName: String, password: String, firstName: String, lastName: String): User {
    return apiClient.identityAPI.createUser(userName, password, firstName, lastName)
}

// Utility function to log in to Bonita with a specific User:
private fun loginWithAnotherUser(newUser: User) {
    apiClient.logout()
    apiClient.login(newUser.userName, "bpm")
}

private fun createAndDeployProcess(initiator: User, validator: User): ProcessDefinition {
    // Create the process:
    val designProcessDefinition = LoanRequestProcessBuilder().buildExampleProcess()
    // Deploy the process and enable it:
    return ProcessDeployer().deployAndEnableProcessWithActor(
            designProcessDefinition, ACTOR_REQUESTER, initiator, ACTOR_VALIDATOR, validator)
} 
```

此时，我们的流程已经创建并部署在 Bonita 中。
让我们通过编写一个列出所有现有进程的 HTTP 端点来检查这一点。
为此，我们需要添加一个简单的 Spring Boot 依赖项及其 JSON 库，以 JSON 格式返回结果:
在这个文件`build.gradle.kts`中，在`dependencies { }`部分

```
 // Libs to expose Rest API through an embedded application server:
    implementation("org.springframework.boot:spring-boot-starter-web:2.1.6.RELEASE")
    implementation("com.fasterxml.jackson.module:jackson-module-kotlin:2.9.8") 
```

现在我们可以编写一个简单的 [Spring MVC 控制器](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html)来通过 HTTP API
公开我们的流程

```
package org.bonitasoft.loanrequest.api

import org.bonitasoft.engine.api.APIClient
import org.bonitasoft.engine.bpm.process.ProcessDeploymentInfo
import org.bonitasoft.engine.search.SearchOptionsBuilder
import org.bonitasoft.loanrequest.process.CONTRACT_AMOUNT
import org.springframework.web.bind.annotation.GetMapping
import org.springframework.web.bind.annotation.PathVariable
import org.springframework.web.bind.annotation.RestController

@RestController
// our apiClient is automagically :-) injected by Spring:
class ProcessController(val apiClient: APIClient) {

    // Expose the deployed processes through Rest APIs:
    @GetMapping("/processes")
    fun list(): List<ProcessDeploymentInfo> {
        apiClient.login("install", "install")
        val result = apiClient.processAPI.searchProcessDeploymentInfos(SearchOptionsBuilder(0, 100).done()).result
        apiClient.logout()
        return result
    }
} 
```

最后，让我们用`./gradlew bootRun`重启我们的应用程序。
我们的应用程序启动、创建和部署我们的流程。
让我们访问[http://localhost:8080/processes](http://localhost:8080/processes)来列出我们的进程。结果应该类似于:

```
[  {  "id":  1,  "name":  "LoanRequest",  "version":  "1.0",  "displayDescription":  null,  "deploymentDate":  "2019-07-05T09:33:21.490+0000",  "deployedBy":  1,  "configurationState":  "RESOLVED",  "activationState":  "ENABLED",  "processId":  7450221031288910000,  "displayName":  "LoanRequest",  "lastUpdateDate":  "2019-07-05T09:33:21.607+0000",  "iconPath":  null,  "description":  null  }  ] 
```

请注意，字段“processId”是不正确的，这是由于 JSON / Javascript `Long`大小的限制，它小于 Java(或 Kotlin)中的`Long`大小。症状是最后的数字是 0000，而不是一个真实的值。
只有当您使用 JSON / Javascript 显示一个`Long`值时，这个限制才会存在。
为了绕过这个限制，我们可以在通过 Rest APIs 返回之前将这个值转换成一个字符串。

### 部署用 Bonita Studio 设计的流程

如果您已经使用 Bonita Studio(图形化工具)设计了您的流程，那么您也可以通过 API 部署生成的`.bar`文件。为此，请将您的。bar 文件，并像这样加载它:

```
class ProcessLoader {
    fun loadProcessFromBar(barFilePath: String): BusinessArchive {
        this.javaClass.getResourceAsStream(barFilePath).use { resourceAsStream ->
            return BusinessArchiveFactory.readBusinessArchive(resourceAsStream)
        }
    }
} 
```

调用“load”函数，然后部署:

```
val processFromBar = ProcessLoader().loadProcessFromBar("/SimpleProcess--1.0.bar")
ProcessDeployer().deployAndEnableBusinessArchiveWithoutAnyActor(processFromBar) 
```

我们可以看到名为`SimpleProcess`的流程现在已经部署:

```
|15:50:59.107|main|INFO |o.b.e.b.BusinessArchiveServiceImpl| THREAD_ID=1 | HOSTNAME=Ubuntu | TENANT_ID=1 | The user <requester> has installed process <SimpleProcess> in version <1.0> with id <6733385816066783268>
|15:50:59.119|BonitaTaskExecutor|INFO |o.b.e.c.ClassLoaderServiceImpl| THREAD_ID=60 | HOSTNAME=Ubuntu | Refreshing classloader with key: PROCESS:6733385816066783268
|15:50:59.200|main|INFO |o.b.e.a.i.t.p.EnableProcess| THREAD_ID=1 | HOSTNAME=Ubuntu | TENANT_ID=1 | The user <requester> has enabled process <SimpleProcess> in version <1.0> with id <6733385816066783268> 
```

完整的例子可以在[源代码](https://github.com/bonitasoft/bonita-examples/blob/master/bonita-loan-request-application/loan-request-app-gradle-kotlin/src/main/kotlin/org/bonitasoft/loanrequest/LoanRequestApplication.kt)中找到。

**警告**:请注意，由特定 Studio 版本生成的. bar 文件只与您在 Gradle dependencies 中导入的 Bonita 版本兼容。如果将依赖项更新到较新的版本，可能需要重新生成。bar 文件与 Bonita Studio 相同的新版本。

### 与流程互动

我们的平台和流程现在可以执行了。我们必须设置与流程的交互，以继续执行流程。
交互取决于我们的流程设计。
下面是申请流程的一个例子:

```
...
executeProcess(requester, validator, processDefinition)

@Throws(BonitaException::class)
fun executeProcess(initiator: User, validator: User, processDefinition: ProcessDefinition) {
    // Start a new Loan request with an amount of 12000.0 (€ Euro):
    val processInstance = apiClient.processAPI.startProcessWithInputs(processDefinition.id, mapOf(Pair(CONTRACT_AMOUNT, 12000.0)))

    // Now the validator needs to review it:
    loginWithAnotherUser(validator)
    // Wait for the user task "Review Request" to be ready to execute, using a user member of "Validator" actor:
    val reviewRequestTask = waitForUserTask(validator, processInstance.id, REVIEW_REQUEST_TASK)

    // Take the task and execute it:
    apiClient.processAPI.assignAndExecuteUserTask(validator.id, reviewRequestTask.id, emptyMap())

    // If the request has been accepted, wait for the "Sign contract" task to be ready and execute it:
    val signContractTask = waitForUserTask(initiator, processInstance.id, SIGN_CONTRACT_TASK)
    apiClient.processAPI.assignAndExecuteUserTask(initiator.id, signContractTask.id, emptyMap())

    // Wait for the whole process instance to finish executing:
    waitForProcessToFinish()

    println("Instance of Process LoanRequest(1.0) with id ${processInstance.id} has finished executing.")
}

// Utility function to wait for a User task to be ready:
fun waitForUserTask(user: User, processInstanceId: Long, userTaskName: String): HumanTaskInstance {
    Awaitility.await("User task should not last so long to be ready :-(")
            .atMost(TEN_SECONDS)
            .pollInterval(FIVE_HUNDRED_MILLISECONDS)
            .until(Callable<Boolean> {
                apiClient.processAPI.getNumberOfPendingHumanTaskInstances(user.id) == 1L
            })
    return apiClient.processAPI.getHumanTaskInstances(processInstanceId, userTaskName, 0, 1)[0]
}

// Utility function to wait for a process to be completed:
fun waitForProcessToFinish() {
    Awaitility.await("Process instance lasts long to complete")
            .atMost(TEN_SECONDS)
            .pollInterval(FIVE_HUNDRED_MILLISECONDS)
            .until(Callable<Boolean> {
                apiClient.processAPI.numberOfArchivedProcessInstances == 1L
            })
} 
```

### 奖励:樱桃顶上

Spring Boot 允许您轻松地调整当应用程序启动时显示的横幅。
简单地在`resources`文件夹中放一个`banner.txt`文件，里面有一些 ASCII 艺术:

```
 _                        ______                           _
| |                       | ___ \                         | |
| |     ___   __ _ _ __   | |_/ /___  __ _ _   _  ___  ___| |_
| |    / _ \ / _` | '_ \  |    // _ \/ _` | | | |/ _ \/ __| __|
| |___| (_) | (_| | | | | | |\ \  __/ (_| | |_| |  __/\__ \ |_
\_____/\___/ \__,_|_| |_| \_| \_\___|\__, |\__,_|\___||___/\__|
                                        | |
                                        |_| 
```

### 在我们的应用流程上公开业务监控

也许我们希望我们的应用程序公开 API，以便能够遵循我们的流程。
简单地添加下面的 Rest 控制器类来公开**运行的**和**完成的**案例(流程实例):

```
package org.bonitasoft.loanrequest.api

import org.bonitasoft.engine.api.APIClient
// ...
import org.springframework.web.bind.annotation.GetMapping
import org.springframework.web.bind.annotation.RestController

@RestController
class CaseController(val apiClient: APIClient) {

    // Expose the open process instances (=cases not completed)
    @GetMapping("/cases")
    fun list(): List<ProcessInstance> {
        apiClient.login("install", "install")
        try {
            return apiClient.processAPI
                    .searchOpenProcessInstances(SearchOptionsBuilder(0, 100).done())
                    .result
        } finally {
            apiClient.logout()
        }
    }

    // Expose the finished process instances (=cases completed)
    @GetMapping("/completedcases")
    fun listCompleted(): List<ArchivedProcessInstance> {
        apiClient.login("install", "install")
        try {
            return apiClient.processAPI
                    .searchArchivedProcessInstances(SearchOptionsBuilder(0, 100).done())
                    .result
        } finally {
            apiClient.logout()
        }
    }

} 
```

类似地，为了公开准备执行的任务，添加下面的 Rest 控制器类:

```
package org.bonitasoft.loanrequest.api

// ...
import org.springframework.web.bind.annotation.PathVariable
import org.springframework.web.bind.annotation.RestController

@RestController
class TaskController(val apiClient: APIClient) {

    @GetMapping("/tasks")
    fun list(): List<HumanTaskInstance>? {
        apiClient.login("install", "install")
        val result = apiClient.processAPI.searchMyAvailableHumanTasks(
                apiClient.session.userId,
                SearchOptionsBuilder(0, 100).done())
                .result
        apiClient.logout()
        return result
    }
} 
```

## 完整示例

[这个库](https://github.com/bonitasoft/bonita-examples/blob/master/bonita-loan-request-application/loan-request-app-gradle-kotlin/)包含了这个例子的全部代码，可以构建/运行了。

## 构建您的应用

运行`./gradlew build`来构建二进制文件。它将在`build/libs/`文件夹中生成一个 jar 文件。

## 运行您的应用程序

只需运行 gradle 命令:

```
./gradlew bootRun 
```

或者运行之前构建的 jar 文件:

```
java -jar build/libs/bonita-loan-request-application.jar 
```

然后，您可以通过打开位于[http://localhost:8080/processes](http://localhost:8080/processes)的 web 浏览器来访问进程列表。
案例(流程实例)列表可在[http://localhost:8080/cases](http://localhost:8080/cases)获得。
已完成的案例(已完成的流程实例)列表可在[http://localhost:8080/completed cases](http://localhost:8080/completedcases)获得。任务列表可在[http://localhost:8080/tasks](http://localhost:8080/tasks)获得。

## 配置数据库

如果未指定任何内容，将创建并使用嵌入式 H2 数据库。
要使用不同的数据库，在文件夹`resources`中创建一个名为`application.properties`的标准 Spring Boot 配置文件(或。yaml)
并设置以下属性。下面是一个 PostgreSQL 的例子:

```
# specify the database vendor you wish to use (supported values are h2, mysql, postgres, sqlserver, oracle): org.bonitasoft.engine.database.bonita.db-vendor=postgres
# specify the URL to connect to your database: org.bonitasoft.engine.database.bonita.url=jdbc:postgresql://localhost:5432/bonita

# specify the connection user to the database: org.bonitasoft.engine.database.bonita.user=bonita
# specify the connection password to the database: org.bonitasoft.engine.database.bonita.password=bpm
org.bonitasoft.engine.database.bonita.datasource.maxPoolSize=3

# specify a different AppServer port if required: server.port=8080 
```

提供的`application.properties`文件包含所有数据库供应商的示例。

最后，不要忘记替换文件`build.gradle.kts` :
中 PostgreSQL 驱动程序的默认 H2 依赖项

```
 // runtime("com.h2database:h2:1.4.199")
    // runtime("mysql:mysql-connector-java:8.0.14")
     runtime("org.postgresql:postgresql:42.2.5")
    // runtime("com.microsoft.sqlserver:mssql-jdbc:7.2.1.jre8")
    // Oracle database drivers are not open-source and thus cannot be included here directly 
```