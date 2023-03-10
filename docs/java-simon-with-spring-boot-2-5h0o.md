# 爪哇西蒙与 Spring Boot 2

> 原文：<https://dev.to/adzubla/java-simon-with-spring-boot-2-5h0o>

Java Simon 是一个简单的监控 API，允许你跟踪和更好地理解你的应用。

在这里，我将展示一些关于如何在 Spring Boot 2 中使用它的技巧。

# 在 pom.xml 中添加 Java Simon 依赖项

```
 <dependency>
            <groupId>org.javasimon</groupId>
            <artifactId>javasimon-spring</artifactId>
            <version>4.2.0</version>
        </dependency>
        <dependency>
            <groupId>org.javasimon</groupId>
            <artifactId>javasimon-jdbc41</artifactId>
            <version>4.2.0</version>
        </dependency>
        <dependency>
            <groupId>org.javasimon</groupId>
            <artifactId>javasimon-console-embed</artifactId>
            <version>4.2.0</version>
        </dependency> 
```

# 弹簧配置

您可以为 JDBC 和 Spring Beans 的 Java Simon 监控添加配置，发布 web 控制台并导出 JMX 的 simons。

### JDBC 监控

```
import org.javasimon.jdbcx4.WrappingSimonDataSource;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.autoconfigure.condition.ConditionalOnProperty;
import org.springframework.boot.autoconfigure.jdbc.DataSourceProperties;
import org.springframework.boot.context.properties.bind.Bindable;
import org.springframework.boot.context.properties.bind.Binder;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;
import org.springframework.core.env.Environment;

import javax.sql.DataSource;

@Configuration
@ConditionalOnProperty("javasimon.jdbc.enabled")
public class SimonJdbcConfiguration {

    private final Environment env;

    @Autowired
    public SimonJdbcConfiguration(Environment env) {
        this.env = env;
    }

    @Primary
    @Bean
    public WrappingSimonDataSource primaryDataSource(DataSourceProperties properties) {
        DataSource dataSource = properties.initializeDataSourceBuilder().build();
        Binder binder = Binder.get(env);
        binder.bind("spring.datasource.hikari", Bindable.ofInstance(dataSource).withExistingValue(dataSource));

        WrappingSimonDataSource wrapper = new WrappingSimonDataSource();
        wrapper.setDataSource(dataSource);
        wrapper.setPrefix(env.getProperty("javasimon.jdbc.prefix", "jdbc"));
        return wrapper;
    }

} 
```

(如果你知道一个更好的方法来布置 Spring Boot 的数据源而不依赖于光，请告诉我！)

### 春季监测

```
import org.springframework.boot.autoconfigure.condition.ConditionalOnProperty;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.ImportResource;

@Configuration
@ConditionalOnProperty("javasimon.spring.enabled")
@ImportResource("classpath:javasimon-spring.xml")
public class SimonSpringConfiguration {
} 
```

将`javasimon-spring.xml`添加到您的类路径:

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">

    <bean id="javasimonInterceptor" class="org.javasimon.spring.MonitoringInterceptor"/>

    <aop:config>
        <aop:pointcut id="monitoringPointcut" expression="execution(* *ServiceImpl(..))"/>
        <aop:advisor pointcut-ref="monitoringPointcut" advice-ref="javasimonInterceptor"/>
    </aop:config>

</beans> 
```

更多关于 Spring AOP 和 Java Simon:
[https://github . com/virgo 47/javasimon/blob/master/Spring/doc/monitoring . XML](https://github.com/virgo47/javasimon/blob/master/spring/doc/monitoring.xml)

### Web 控制台

```
import org.javasimon.console.SimonConsoleFilter;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.autoconfigure.condition.ConditionalOnProperty;
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.env.Environment;

@Configuration
@ConditionalOnProperty("javasimon.console.enabled")
public class SimonConsoleConfiguration {

    private final Environment env;

    @Autowired
    public SimonConsoleConfiguration(Environment env) {
        this.env = env;
    }

    @Bean
    public FilterRegistrationBean<SimonConsoleFilter> simonFilter() {
        FilterRegistrationBean<SimonConsoleFilter> registrationBean = new FilterRegistrationBean<>();

        String urlPrefix = env.getProperty("javasimon.console.prefix", "simon");

        registrationBean.setFilter(new SimonConsoleFilter());
        registrationBean.setName("simon-console-filter");
        registrationBean.addUrlPatterns("/" + urlPrefix + "/*");
        registrationBean.addInitParameter("url-prefix", "/" + urlPrefix);

        return registrationBean;
    }

} 
```

### JMX 汇报

你可以在任何类似`jconsole`的 JMX 工具中看到西蒙的信息。

```
import org.javasimon.jmx.JmxReporter;
import org.springframework.boot.autoconfigure.condition.ConditionalOnProperty;
import org.springframework.context.annotation.Configuration;

import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;

@Configuration
@ConditionalOnProperty("javasimon.jmx.enabled")
public class SimonJmxConfigurer {

    private JmxReporter reporter;

    @PostConstruct
    public void start() {
        reporter = JmxReporter.forDefaultManager().registerSimons().registerExistingSimons().start();
    }

    @PreDestroy
    public void stop() {
        reporter.stop();
    }

} 
```

# 应用属性

所有这些都可以在`application.properties`文件中启用/禁用。如果属性不存在，默认情况下将禁用该功能。

```
javasimon.jdbc.enabled=true
javasimon.spring.enabled=true
javasimon.console.enabled=true
javasimon.jmx.enabled=true 
```

web 控制台的 url 默认为“simon”，jdbc simons 的名称前缀是“jdbc”。这可以通过以下属性进行更改:

```
javasimon.jdbc.prefix=jdbc
javasimon.console.prefix=simon 
```