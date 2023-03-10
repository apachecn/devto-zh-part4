# 使用 Spring Boot 创建一个 GraphQL API

> 原文：<https://dev.to/fabiothiroki/create-a-graphql-api-using-spring-boot-32o2>

# 简介

在本教程中，我们将构建一个 Pokemon API，它使用 Postgres 数据库中的数据，并带有一个简单的端点，通过 id 执行搜索。

最终代码在这个 [Github 库](https://github.com/fabiothiroki/spring-boot-pokedex-graphql)里。

# Postgres 设置

如果您已经在本地安装了 Postgres，您可以跳过这一部分，否则最简单的方法就是运行 Docker 映像。只需安装 [Docker](https://docs.docker.com/install/) 然后:

```
docker run -p5432:5432 -d postgres:11.4-alpine 
```

Enter fullscreen mode Exit fullscreen mode

这个命令将使用默认用户`postgres`和默认数据库`postgres`在端口 5432 上启动一个 Postgres 实例。

# Spring Boot 设置

我们将从使用 [Spring Initializr](https://start.spring.io/) 创建初始项目文件开始。我选择了:

*   Gradle
*   Java 语言(一种计算机语言，尤用于创建网站)
*   Spring Boot
*   弹簧网启动器
*   春季数据 JPA
*   PostgreSQL 驱动程序

除了 Spring 依赖项，我们还需要添加 GraphQL 库:

*   GraphQL Spring Boot 启动器:将自动创建一个`/graphql`端点
*   GraphQL Spring Boot 入门测试:用于我们的单元测试
*   GraphQL Java 工具:来自它自己的[文档](https://www.graphql-java-kickstart.com/tools/schema-definition/):“将 GraphQL 对象上的字段映射到 Java 对象上的方法和属性”。这个库需要版本为`1.3.*`的 Kotlin，所以您需要在项目根目录下创建一个`gradle.properties`文件，内容为:

```
kotlin.version=1.3.10 
```

Enter fullscreen mode Exit fullscreen mode

## 数据库连接

添加完依赖项后，您可以编辑`src/main/resources/application.properties`文件来添加 Postgres 配置。如果您使用上面的 Docker 命令在本地启动 Postgres，您的文件应该是这样的:

```
## PostgreSQL
spring.datasource.url=jdbc:postgresql://localhost:5432/postgres
spring.datasource.username=postgres
spring.datasource.password=

#drop n create table again, good for testing, comment this in production
spring.jpa.hibernate.ddl-auto=create-drop 
```

Enter fullscreen mode Exit fullscreen mode

运行您的应用程序来测试到目前为止是否一切正常:`./gradlew bootRun`。

# GraphQL 模式

GraphQL 有一个很棒的模式语言，它将类型声明添加到请求和返回值中，并将其耦合到 API 实现中。这意味着必须实现您在模式中声明的内容。

如果我们想添加一个端点来搜索口袋妖怪的 id，我们应该在`src/main/resources/schema.graphqls`文件中声明:

```
type  Pokemon  {  id:  ID!  name:  String!  }  type  Query  {  pokemon(id:  ID!):  Pokemon  } 
```

Enter fullscreen mode Exit fullscreen mode

我们现在的下一步必须是通过 id 对 Pokemon 实例进行数据库搜索，否则应用程序将无法运行。

# 查询解析器

声明的模式期望返回一个包含必需属性`id`和`name`的口袋妖怪`type`。

对于我们的应用程序，这意味着`Pokemon`是一个具有`id`和`name`属性的 Java 类，但也是一个数据库表。我们可以使用`javax.persistence`注释将口袋妖怪自动映射到带有`id`和`name` :
列的数据库表

```
@Entity
@Table(name = "pokemon")
public class Pokemon {

    public Pokemon(final Long id, final String name) {
        this.id = id;
        this.name = name;
    }

    @Id
    public Long id;

    @Column
    public String name;
} 
```

Enter fullscreen mode Exit fullscreen mode

另一个期望的类应该是一个实现`GraphQLQueryResolver`接口的`Spring Bean`，并且应该有一个名为`getPokemon`的方法，该方法与我们在方案中定义的参数和响应完全匹配:

```
@Component
public class Query implements GraphQLQueryResolver {

    public Pokemon getPokemon(Long id) {
        return new Pokemon(1L, "Pikachu");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以在新的端点上执行一个请求，检查它的响应是否是我们的皮卡丘。

[![Pikachu appearing](img/47e29038cdc087105515b61d97021338.png)](https://i.giphy.com/media/xuXzcHMkuwvf2/giphy.gif)

# 图 QL

GraphiQL 在我们的 API 上配置了一个端点，允许我们测试任何查询。在我们的项目中，它将在地址`http://localhost:8080/graphiql`上运行。

左栏是我们应该写查询的地方，右栏是结果。例如，如果我们输入查询:

```
# Searches a Pokemon with id 25 and returns its field 'name'  query  {  pokemon(id:  25){  name  }  } 
```

Enter fullscreen mode Exit fullscreen mode

我们应该期待右栏的结果:

```
{  "data":  {  "pokemon":  {  "name":  "Pikachu"  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，我们传递哪个参数`id`并不重要，因为我们已经固定了响应对象，但是现在我们将实现数据库搜索。

# 从数据库中取出口袋妖怪

目前，我们的应用程序没有进行真正的数据库搜索，而是返回一个固定的实例。现在让我们实现这一部分。

首先我们创建一个扩展了`JpaRepository` :
的`PokemonRepository`接口

```
@Repository
public interface PokemonRepository extends JpaRepository<Pokemon, Long> {
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们修改我们的`Query`类来自动连接这个 bean 并执行真正的数据库获取:

```
@Component
public class Query implements GraphQLQueryResolver {

    @Autowired
    private PokemonRepository repository;

    public Pokemon getPokemon(Long id) {
        // Not returning a fixed instance anymore
        return repository.findById(id).orElse(null);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 单元测试

我们的自动化测试将利用`GraphQLTestTemplate`类，它允许我们输入一个`query`并验证它的响应。例如，如果我们想通过 id 查询测试搜索 pokemon，我们首先必须用这个查询在`src/test/resources`中创建一个文件:

```
# src/test/resources/get-pokemon-by-id.graphql  query  {  pokemon(id:  "1")  {  id  name  }  } 
```

Enter fullscreen mode Exit fullscreen mode

测试类应该用`@GraphQLTest`注释，这样它可以解析`GraphQLTestTemplate`实例，而`PokemonRepository`应该用`@MockBean`注释，这样我们可以用`Mockito`模拟它的响应。

```
@RunWith(SpringRunner.class)
@GraphQLTest
public class DemoApplicationTests {

    @Autowired
    private GraphQLTestTemplate graphQLTestTemplate;

    @MockBean
    private PokemonRepository pokemonRepository;

    @Test
    public void getById() throws IOException {
        Pokemon pokemon = new Pokemon(1L, "Pikachu");
        when(pokemonRepository.findById(any()))
                .thenReturn(Optional.of(pokemon));

        GraphQLResponse response =
                graphQLTestTemplate.postForResource("get-pokemon-by-id.graphql");

        assertTrue(response.isOk());
        assertEquals("1", response.get("$.data.pokemon.id"));
        assertEquals("Pikachu", response.get("$.data.pokemon.name"));
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

基本上，我们在这里测试的场景如下:

*   假设存储库在调用`findById`方法时返回一个皮卡丘
*   当我们用`get-pokemon-by-id.graphql`查询 GraphQL Api 时
*   然后我们期望响应是一个 JSON，包含来自存储库的皮卡丘

# 结论

使用 Spring Boot 实现 GraphQL Api 的挑战主要依赖于 Spring Boot 功能的配置和小细节。总的来说，我认为集成工作得很好，特别是 GraphQL Java 工具，它加强了代码的实现。