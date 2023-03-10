# 使用 Spring Cloud Kubernetes 的外部应用配置

> 原文：<https://dev.to/jmlw/external-application-config-with-spring-cloud-kubernetes-168n>

[![External Application Config with Spring Cloud Kubernetes](img/371fb6743744bc371e2f053dd85d2582.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yAyyzDJy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1555611206-10075b5b7580%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

将应用程序部署到开发、试运行和生产环境时，一个常见的模式是一次性构建一个 jar 或 docker 映像，然后在每个阶段的部署中提供不同的配置值。这种配置可以是单独的 yaml 文档中的 Spring 概要文件、附加的属性文件、环境变量或其他一些配置机制。

当部署到 Kubernetes 时，配置 Spring 应用程序变得有点困难。仍然可以选择使用配置文件运行我们的应用程序，只需“启用”该配置文件特定的 application.yml。缺点是我们的所有配置都部署在 docker 映像中，因此更新它需要新的部署。我们仍然可以选择在 Kubernetes 部署 yaml 中配置环境变量，并让 Kubernetes 将提供的值映射到为运行我们的应用程序而创建的容器中。直接集成到 Spring bootstrap 过程中的一个更好的选择是利用 Spring Cloud Kubernetes 配置和存储在集群中的配置映射。这允许我们在 Kubernetes *ConfigMap* 中定义一个特定于环境的`application.yml`，Spring 将自动查找数据并将其合并到现有的配置属性中。这种方法的额外好处是，更改配置只需要更新 ConfigMap 并重启 Spring 上下文来读取新属性。

> 注意:这篇文章需要访问本地运行的 Kubernetes 集群或 Minikube，并且希望你对 Kubernetes 有一些操作知识。如果您无法访问 Kubernetes，您可以按照 Kubernetes.io 的[官方说明安装 Minikube。](https://kubernetes.io/docs/tasks/tools/install-minikube/)

# 创建和配置项目

我们需要一个项目开始，所以前往[春季初始化](https://start.spring.io/)生成一个新项目。在那里，选择 *Spring Web Starter* 作为唯一的依赖项，并确保设置了 Java、1.8 版和最新的 Spring 版本。出于我们的目的，我们不需要项目创建中的任何额外内容，稍后会添加其他依赖项。我们选择了 web 支持，这样我们就有了一个可以测试的 API，而不是仅仅依赖应用程序日志进行验证；当调用一个 API 时，看到一个项目工作比阅读日志更有趣。如果您不希望从头开始设置项目，那么您可以克隆[演示报告](https://github.com/jmlw/demo-projects.git)并导航到`spring-cloud-kubernetes-config-demo`目录以查看完成的项目。

# 加春云相依

现在我们有了一个支持 web 的基本项目，我们需要再添加一个依赖项来允许 Spring 从 Kubernetes: `spring-cloud-kubernetes-config` with `groupId: org.springframework.cloud`读取配置图和秘密。如果你使用的是 Maven，你的 pom 应该类似于这个(为了简洁省略了部分内容):

```
<dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-kubernetes-config</artifactId>
        </dependency>
        ...
    </dependencies> 
```

如果你不熟悉 spring cloud，我们还有一个东西要添加到 pom 中以使其正确编译，依赖项管理部分允许来自特定发布系列的 Spring Cloud 依赖项集成到我们的项目中:

```
<dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR2</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement> 
```

那么它对我们有什么作用呢？`spring-cloud-kubernetes-config`依赖关系是`spring-cloud-starter-kubernetes`家族中的一员。它与 Spring bootstrap 进程挂钩，从 Kubernetes ConfigMap 和 Secret 中提供一个额外的属性源，这些属性源与我们在`application.yml`中配置的`spring.application.name`同名。此外，它不需要 bean 或项目中的任何额外配置；自动配置负责实例化配置 bean，一旦将依赖项添加到项目中，就可以透明地进行设置。关于 [Spring Cloud Kubernetes 项目的更多信息可以在 GitHub](https://github.com/spring-cloud/spring-cloud-kubernetes) 上找到。

厉害！我们现在可以收工了。我们做到了，很光荣！

[![External Application Config with Spring Cloud Kubernetes](img/6c382db989726299258db7cff65d9ddd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DzCfkRwl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1436076863939-06870fe779c2%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ) 

<figcaption>照片由[威尔·斯图尔特](https://unsplash.com/@wilstewart3?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) / [Unsplash](https://unsplash.com/?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit)</figcaption>

拍摄

不完全是。我们仍然需要使用依赖项，并添加一些配置来验证它是否按预期工作。当然，我们需要部署到 Kubernetes。那么，接下来...

# 弹簧配置

让我们给我们的`application.yml`添加一些东西。首先，确保应用程序名称被设置为`spring-cloud-kubernetes-config-demo`，这样您的项目将与演示和本教程相匹配。现在向 yaml 添加几个应用程序配置键:

```
app:
  config: Default value
  environmentVariable: ${ENVIRONMENT_CONFIG:Default value} 
```

在这个代码片段中，`config`键在所有情况下都被设置为“默认值”,但是第二个键`environmentVariable`被定义为默认为“默认值”,或者如果在您的主机或应用程序主机上定义了`ENVIRONMENT_CONFIG`,那么将使用该值。

我们将使用这些配置键来演示 Spring 如何在部署/启动时将数据从我们的 Kubernetes 集群和容器环境映射到应用程序中。现在我们需要在某个地方实际使用它们，这样我们就可以看到如何通过 Kubernetes 配置它们。

# 使用 application.yml 配置

验证这些值的最简单方法是创建一个控制器，该控制器在构造过程中也记录这些值。这可以根据您的需要简单或复杂，但是对于我的目的，下面的例子就足够了。

```
@RestController
public class ConfigurableController {
    private static final Logger LOGGER = LoggerFactory.getLogger(ConfigurableController.class);

    private String externalConfig;
    private String environmentVariable;

    public ConfigurableController(
            @Value("${app.config}") String externalConfig,
            @Value("${app.environmentVariable}") String environmentVariable
    ) {
        this.externalConfig = externalConfig;
        this.environmentVariable = environmentVariable;
        LOGGER.info(String.format("app.config: %s\napp.environmentVariable: %s", externalConfig, environmentVariable));
    }

    @GetMapping("/")
    public Map<String, String> getConfig() {
        Map<String, String> config = new HashMap<>();
        config.put("app.config", externalConfig);
        config.put("app.environmentVariable", environmentVariable);
        return config;
    }
} 
```

此时，应用程序应该运行，并在`http://localhost:8080/`有一个可用的端点，它将返回一个响应数据图，其中包含在构建时映射的动态值。

```
{
    "app.config": "Default value",
    "app.environmentVariable": "Default value"
} 
```

现在我们可以创建一些将应用程序部署到 Kubernetes 集群中所需的 Kubernetes 对象。

# 立方结构图(s)

我们将利用 Spring Cloud Kubernetes 配置依赖项使用的默认配置；默认情况下，如果我们可以检测到应用程序正在 Kubernetes 中运行，就寻找与我们的应用程序同名的 ConfigMap。因此，我们需要创建一个 yaml 来表示我们的配置图。因为我们将使用 Spring 的默认配置来搜索与我们的 Spring 应用程序名称同名的 ConfigMap，所以请确保此处的 ConfigMap 名称与 application.yml 中定义的名称相匹配。

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: spring-cloud-kubernetes-config-demo 
```

从技术上来说，这就是所需要的，但是它不提供任何配置，更不用说对我们的 Spring 应用程序有用的任何东西了。我们可以在 yaml 中添加一个顶级的`data`键，在这里我们可以玩我们喜欢的任何配置。

```
# app-config.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: spring-cloud-kubernetes-config-demo
data:
  application.yaml: |-
    app:
      config: Configuration from Kubernetes! 
```

这个例子中的数据键有几个重要的特性；首先，它有一个嵌套键，在本例中是“application.yaml ”,该键使用管道连字符(`|-`)来表示嵌套在它下面的所有值，它是一个块，代表一个多行值。更多信息，请参见(Block chopping Indicator YAML-multiline . info)[【https://yaml-multiline.info/ T2】。重要的是，它允许我们在这个 ConfigMap 的一个键中定义 application.yml 的所有自定义值。这个例子中不明显的另一个要点是，由于`application.yaml`是数据段中唯一的键，所以它的名称并不重要。事实上，我们可以这样做:

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: spring-cloud-kubernetes-config-demo
data:
  some-configs-here: |-
    app:
      config: Configuration from Kubernetes! 
```

在这种情况下，名称并不重要。然而，如果我们希望使用 ConfigMap 不仅仅是存储特定于环境的应用程序 yaml，比如用于启动我们的应用程序的 bash 脚本的环境变量，或者 Spring 启动之前需要的一些其他重要配置，那么我们**必须将关键应用程序命名为. yaml** 。如果我们不这样做，那么 spring-cloud-kubernetes-config 将无法找到映射到 spring 的复合属性源的相关数据，因此我们将不会在启动时将预期的配置值应用到我们的应用程序。

在这个过程中，我们还可以创建另一个 ConfigMap 来存储一个值，稍后我们可以使用这个值通过环境变量映射到我们的应用程序中。我们可以使用 Kubernetes 部署将配置图中的值注入到正在运行的容器中，如下所示。

```
# environment-variable-config.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: environment-variable-config
data:
  ENVIRONMENT_CONFIG: Configuration from Docker environment in Kubernetes! 
```

# Kubernetes 部署

既然我们已经定义了配置，我们可以继续创建部署。正如本文开头所提到的，这是假设你已经掌握了一些 Kubernetes 知识。因此，我们将配置一个非常简单的部署，它应该允许访问应用程序，而不需要额外的基础架构，如入口，或者比对部署应用程序的节点的 IP 进行网络访问更复杂的任何东西。为了实现这一点，我们将创建一个公开应用程序端口的部署，以及一个配置 Kubernetes 以公开节点端口并将节点上的端口映射回应用程序的服务。

```
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo-deployment
  labels:
    app: demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: demo
  template:
    metadata:
      labels:
        app: demo
    spec:
      serviceAccountName: demo-service-account
      containers:
        - name: spring-cloud-kubernetes-config-demo
          image: jmlw/spring-cloud-kubernetes-config-demo
          imagePullPolicy: Never
          ports:
            - containerPort: 8080
          env:
            - name: ENVIRONMENT_CONFIG
              valueFrom:
                configMapKeyRef:
                  name: environment-variable-config
                  key: ENVIRONMENT_CONFIG
---
kind: Service
apiVersion: v1
metadata:
  name: demo
spec:
  selector:
    app: demo
  ports:
    - protocol: TCP
      port: 8080
      nodePort: 30000
      name: http
  type: NodePort 
```

我们在这里将我们的应用程序命名为`demo`，我们希望 Kubernetes 在本地找到应用程序的 docker 图像。这意味着我们必须从 Kubernetes 使用的相同 docker 上下文中构建源代码。如果您正在使用 Minikube，您可以通过运行以下命令，从 Minikube 轻松地将当前终端附加到 docker 上下文。

```
eval $(minikube docker-env) 
```

既然我们的终端应该配置为在 Minikube 环境中运行 docker 命令，我们可以在本地构建应用程序，然后尝试部署它。我已经在示例项目中配置了 maven，以包含 Spotify Dockerfile 插件，这样我们就可以使用熟悉的工具轻松构建 docker 映像。

```
<build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <plugin>
                <groupId>com.spotify</groupId>
                <artifactId>dockerfile-maven-plugin</artifactId>
                <version>1.4.9</version>
                <configuration>
                    <repository>${dockerhub.username}/${project.artifactId}</repository>
                    <buildArgs>
                        <JAR_FILE>target/${project.build.finalName}.jar</JAR_FILE>
                    </buildArgs>
                </configuration>
            </plugin>
        </plugins>
    </build> 
```

有了这个插件，我们可以运行`./mvnw clean compile package dockerfile:build`，它会将我们的应用程序构建到一个名为`jmlw/spring-cloud-kubernetes-config-demo:latest`的 docker 映像中。

在继续之前，我们需要为我们的应用程序再定义一件事，以便我们的 Spring Cloud Kubernetes 配置依赖能够完成它的工作。默认情况下，当前版本的 Kubernetes 启用 RBAC(基于角色的访问控制)，因此您需要授予我们的部署对 Kubernetes APIs 的显式访问权限，它将需要发现配置映射和应该允许它读取的 Secretes。

```
# rbac.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: demo-role
rules:
  - apiGroups: [""] # "" indicates the core API group
    resources: ["pods", "configmaps"]
    verbs: ["get", "watch", "list"]

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: RoleBinding
metadata:
  name: demo-role-binding
  namespace: default
subjects:
  - kind: ServiceAccount
    name: demo-service-account
    namespace: default
roleRef:
  kind: Role
  name: demo-role
  apiGroup: rbac.authorization.k8s.io

---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: demo-service-account
  namespace: default 
```

使用此 rbac.yaml，我们可以授予服务帐户“demo-service-account”对 pod 和 ConfigMaps 的读取权限。如果需要或者想要，还可以在角色定义中的资源列表中添加“secretes”。

一旦构建了 docker 映像，我们就可以使用`kubectl`将我们定义的 yamls 应用到我们的 Kubernetes 集群，并观察应用程序的启动和自我配置。为了实际部署，您可以创建上面列出的 yaml 文件，然后运行`kubectl apply -f rbac.yaml environment-variable-config.yaml app-config.yaml deployment.yaml`。

否则，如果您跟踪来自[演示项目库](https://github.com/jmlw/demo-projects/tree/master/spring-cloud-kubernetes-config-demo)的源代码，那么您可以通过`kubectl apply -f deployments/`应用相同的结果 yaml 清单，这将在部署目录中部署所有 YAML。

> 注意:如果你想跳过在本地或在你的 Kubernetes 集群中构建应用程序，你可以将 ImagePullPolicy 切换到“总是”,这将使 Kubernetes 从 Dockerhub 中获取“最新的”

# 验证

首先，确保我们部署的 pod 已经启动并且是健康的:

```
kubectl get pods

# expected:
# NAME READY STATUS RESTARTS AGE
# spring-cloud-kubernetes-config-demo 1/1 Running 1 2m 
```

如果应用程序没有运行，调试应用程序启动失败的原因超出了本文的范围。但是，最可能的原因是 1)缺少部署中定义的 env 变量，该变量依赖于对命名配置映射的引用，docker 映像缺少或与您的主机不兼容，或者 3) Java/Spring 无法启动，这可能是配置问题。

现在应用程序已经启动，检查来自 Kubernetes 的日志:

```
kubectl logs "$(kubectl get pods | grep demo-deployment | awk '{print $1}')" 
```

您应该会看到一些从我们的控制器构造中打印出来的日志语句，如下所示:

```
2019-08-31 23:25:48.459 INFO 46773 --- [main] c.j.s.ConfigurableController : app.config: Configuration from Kubernetes!
app.environmentVariable: Configuration from Docker environment in Kubernetes! 
```

现在我们实际上可以调用应用程序的端点了。如果您在 Minikube 中运行，您可以运行下面的代码，它将调用 Minikube 中我们的服务名 demo 的 URL 上的根端点。

```
curl "$(minikube service demo --url)"
# expected output (or similar):
# {
# "app.config":"Configuration from Kubernetes!",
# "app.environmentVariable":"Configuration from Docker environment in Kubernetes!"
# } 
```

# 附加配置选项

除了在单个 ConfigMap 的 application.yaml 键或 ConfigMap 的唯一键中进行映射的基本选项之外，还可以配置 Spring 从当前名称空间之外的名称空间中搜索附加的 config map。还有其他可用的配置选项，您可以在 Spring Cloud Kubernetes 资源库的 GitHub 上找到[。我还没有在生产环境中尝试过的一个有趣的选项是使用设置为 true 的`spring.cloud.kubernetes.reload.enabled`值。这允许 Spring 根据`spring.cloud.kubernetes.reload.strategy`热重新加载配置属性，T1 可以是`refresh`、`restart_context`或`shutdown`。](https://github.com/spring-cloud/spring-cloud-kubernetes)

你可能会有一个挥之不去的问题，为什么不像上面的`ENVIRONMENT_CONFIG`那样映射环境变量呢？老实说，这在大多数情况下都很简单，除非你的 application.yml 对几乎每个键都有特殊的配置。通过环境变量映射这些的最大缺点是所有的配置都定义了三次；一次在 application.yml 中，一次在 deployment.yaml 中，一次在 configmap.yaml 中。这就留下了三个可能导致错误配置或更糟的应用程序崩溃的地方。否则，依靠一点 spring 魔法，您可以只使用 ConfigMap 和 application.yaml 键来提供配置，这些键也不需要存在于打包的 application . yml 中。在我看来，这对避免重复、拼写错误或无法更新配置值的巨大好处来说，是一种稍微高一点的认知开销。

和往常一样，可以在我的 [demo-projects 库](https://github.com/jmlw/demo-projects/tree/master/spring-cloud-kubernetes-config-demo)中找到完整的工作演示。任何疑问或问题，随时打开一个问题，我会尽快审查。

快乐的编码和航行在库伯内特斯海，带着你靴子里的小弹簧！