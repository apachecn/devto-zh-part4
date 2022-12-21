# 5 分钟内 JFrog Artifactory REST API

> 原文：<https://dev.to/jfrog/jfrog-artifactory-rest-api-in-5min-175i>

[![A good API is like a classic car - You want to use it again and again.](img/5eda28fbdbd956eff24ecf26a4edd988.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Vcf1-e1c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://greenido.files.wordpress.com/2019/08/screen-shot-2019-08-13-at-4.39.38-pm.png%3Fw%3D696) 

<figcaption>一个好的 API 就像一辆老爷车——你想一次又一次地使用它。</figcaption>

与 Artifactory 的大多数交互将来自您的 CI/CD 工具。它可能是您的构建引擎或者来自您的日志聚合器。这个[强大的 API](https://www.jfrog.com/confluence/display/RTF/Artifactory+REST+API#ArtifactoryRESTAPI-Example-DeployinganArtifact) 可以以你喜欢的任何标准方式调用，与任何其他 RESTful APIs(例如 curl、CLI、你的源代码等)一起工作。

在许多情况下，当谈到自动化时，它是开发人员首选的“粘合剂”。选项是广泛的，您可以用这个 API 做许多有用的事情。然而，在这篇短文中，我们将涵盖你“必须拥有”的最受欢迎的行为。

先说最常见的动作:“**上传/下载二进制文件**”。此操作可以使用
从构建机器自动运行到 Artifactory

```
curl -u myUser:myP455w0rd! -X PUT "http://localhost:8081/artifactory/my-repository/my/new/artifact/directory/myAppBuild.exe" -T ./myAppBuild.exe And with the JFrog CLI: jfrog rt u ./myAppBuild.exe my-repository/my/new/artifact/directory/ 
```

至于 can see，您可以使用任何您喜欢的技术中的 REST API。一个很好的选择是使用 **[JFrog CLI](https://www.jfrog.com/confluence/display/CLI/JFrog+CLI)** 。

它是一个紧凑的客户端(用 Go 编写)，提供了一个简单的接口来自动访问 Artifactory。作为 API 的包装器，它提供了一种简化自动化脚本的方法，使它们更可读、更易于维护，并行上传和下载、校验和优化以及通配符/正则表达式等功能使您的脚本更高效、更可靠。

**下载神器**是 GET 命令的一行:

```
curl -u myUser:myP455w0rd! -X GET http://localhost:8081/artifactory/libs-release-local/ch/qos/logback/logback-classic/0.9.9/logback-classic-0.9.9.jar And with JFrog CLI: jfrog rt dl libs-release-local/ch/qos/logback/logback-classic/0.9.9/logback-classic-0.9.9.jar 
```

### 搜索

你会惊讶地发现，Artifactory 将会多么快地成为你所有二进制文件的真实来源，以及你拥有的二进制文件的数量。如果您希望找到某个包/库或构建文件，有一个强大的搜索 API。你可以像这样做一些快速搜索:

```
curl -u myUser:myP455w0rd! http://localhost:8081/artifactory/api/search/artifact?name=lib&repos=libs-release-local OR with JFrog CLI: jfrog rt s libs-release-local/lib { "results": [{ "uri": "http://localhost:8081/artifactory/api/storage/libs-release-local/org/acme/lib/ver/lib-ver.pom" },{ "uri": "http://localhost:8081/artifactory/api/storage/libs-release-local/org/acme/lib/ver2/lib-ver2.pom" }] } 
```

您还可以搜索并找到感兴趣的数据点，例如在下面的示例中，我们希望查询所有来自构建(在本例中是 Jenkins)并且大于 100Mb 的文件。

```
// Put this: items.find( { "type":"file", "created\_by":"jenkins", "size":{"$gt":"100000"} }) .sort({"$desc":["size","name"]}) .limit(50) // Add the query to file: aql-query-1.txt so in the next command, You can execute it. // // Search for the biggest files that our Jenkins built. // You can change it and tune the size/type of the files. // Let's use curl to get the results curl -uYourUserName:YourPassword -X POST http://ArtifactoryUrl/artifactory/api/search/aql -d @aql-query-1.txt -H \"content-type: text/plain\""; 
```

您将得到一个类似于
的 JSON 响应

```
{ "results" : [{ "repo" : "docker-prod-local", "path" : "docker-app/65", "name" : "sha256\_\_a56a07bf8a7e40e8580e26b5c7ad51e88e130cce5537bd53aec960de3e9f4e3f", "type" : "file", "size" : 182286302, "created" : "2019-06-10T22:59:11.432Z", "created\_by" : "jenkins", "modified" : "2019-06-10T22:59:08.982Z", "modified\_by" : "jenkins", "updated" : "2019-06-10T22:59:11.433Z" },{ "repo" : "docker-prod-local", "path" : "docker-framework/69", "name" : "sha256\_\_a56a07bf8a7e40e8580e26b5c7ad51e88e130cce5537bd53aec960de3e9f4e3f", "type" : "file", "size" : 182286302, "created" : "2019-06-10T22:57:22.855Z", "created\_by" : "jenkins", "modified" : "2019-06-10T22:57:19.454Z", "modified\_by" : "jenkins", "updated" : "2019-06-10T22:57:22.856Z" },{ "repo" : "docker-stage-local", "path" : "docker-app/65", "name" : "sha256\_\_a56a07bf8a7e40e8580e26b5c7ad51e88e130cce5537bd53aec960de3e9f4e3f", "type" : "file", "size" : 182286302, "created" : "2019-06-10T22:59:11.432Z", "created\_by" : "jenkins", "modified" : "2019-06-10T22:59:08.982Z", "modified\_by" : "jenkins", "updated" : "2019-06-10T22:59:11.433Z" }], "range" : { "start\_pos" : 0, "end\_pos" : 3, "total" : 3, "limit" : 3 } } 
```

现在，您可以获取这些信息，并获得关于每个 docker 映像或您正在使用的任何其他包、库、工件的更多数据。

### 码头工人

Docker 现在很流行，所以让我们来看看所有 Docker 库的列表:

```
curl -u youUserName:youPassWord http://YouArtifactoryServer.com/artifactory/api/docker/docker-stage-local/v2/\_catalog // The response: { "repositories" : ["docker-local-apps", "docker-prod-frameworks"] } 
```

接下来，我们可以获取指定的 **[Artifactory Docker 仓库](https://jfrog.com/integration/docker-registry/docker-repository/)** :
的所有标签的列表

```
// GET /api/docker/{repo-key}/v2/{image name}/tags/list?n=\<n from the request&gt;&amp;last=\<last tag value from previous response&gt; curl -u youUserName:youPassWord http://YouArtifactoryServer.com/artifactory/api/docker/docker-prod-local/v2/docker-app/tags/list // The response: { "name" : "docker-app", "tags" : ["59", "60", "61", "62", "63", "64", "65", "66", "67", "68"] } 
```

### 完整示例

看看这个: