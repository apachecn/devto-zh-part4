# 使用 docker 容器生成 jooq 类

> 原文：<https://dev.to/rohithmenon89/generate-jooq-classes-using-docker-containers-4g62>

Tech stack - #java、#maven、#liquibase、#docker

# 为什么？

我将谈谈我们为实现以下目标所做的工作，

1.  从内存或临时数据库中生成 jooq 类，而不是连接到 prelive/live 环境。

2.  如何在生成 jooq 类之前使用 liquibase 应用所有的迁移？

3.  基于 Postgres 驱动生成 jooq 类。Jooq 支持生成连接到 h2(内存数据库)的类，但不支持 Postgres。我们通常使用 Postgres，而 h2 通常不支持 Postgres 的许多特性。

4.  避免使用多个 maven 插件和 100 行代码，而是使用一个 maven 插件。

# 我们做了什么

1.  在 maven 预编译阶段启动“测试容器”。([https://www.testcontainers.org/#about](https://www.testcontainers.org/#about)

2.  在测试容器上应用 liquibase 迁移。

3.  根据提供的模式生成 jooq 类。

# 哪里可以找到

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[【jango 89】](https://github.com/jango89)/[【jooqgen-liquibase-posters】](https://github.com/jango89/jooqgen-liquibase-postgres)

### Maven 插件，带有 jooq、liquibase 和 postgres

<article class="markdown-body entry-content container-lg" itemprop="text">

# 这是什么

可以集成到任何 Maven 项目示例中的 maven 插件:

```
<plugin>  
    <groupId>com.mytaxi</groupId>  
     <artifactId>jooqgen-liquibase-postgres</artifactId>
    <configuration>
        <schema>bookingoptionsservice</schema> <-- schema name -->
        <packageName>com.mytaxi.bookingoptionsservice</packageName> <-- package to be created for generated classes -->
        <liquibaseChangeLogFile>${liquibase.changeLogFile}</liquibaseChangeLogFile> 
    </configuration>
    <executions>
         <execution>
             <phase>generate-sources</phase>
            <goals>
                 <goal>jooqOverPostgresContainer</goal>
            </goals>
        </execution>
    </executions>
 </plugin> 
```

# 它的作用

1.  启动一个 postgress docker 容器。
2.  对容器应用 liquibase 更改。
3.  为连接到 postgres 容器的源项目生成 JOOQ 类。

# 问题和解决方案

如果生成的类无法编译，

1.  将 `/target/generated-sources/jooq/`文件夹包含到相应的编译器插件中。
2.  如果 kotlin-maven-plugin 编译失败，添加

    ```
    <configuration>
        <sourceDirs>
            <source>src/main/java</source>
            <source>target/generated-sources/jooq</source>
        </sourceDirs>
    </configuration> 
    ```

3.  如果`NoClassDefError`发生，这意味着类文件丢失。添加以下插件

```
 <plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>build-helper-maven-plugin</artifactId>
    <executions>
        <execution>
            <phase>generate-sources</phase>
            <goals>
                <goal>add-source</goal>
            </goals>
            <configuration>
                <sources>
                    <source>${project.build.directory}/generated-sources/jooq</source>
                </sources>
            </configuration>
        </execution>
    </executions>
 </plugin> 
```

</article>

[View on GitHub](https://github.com/jango89/jooqgen-liquibase-postgres)