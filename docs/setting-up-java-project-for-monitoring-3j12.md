# 设置用于监控的 Java 项目

> 原文：<https://dev.to/madhur/setting-up-java-project-for-monitoring-3j12>

在部署用于监控的任何 Java 应用程序中，建立适当的监控/警报基础设施非常重要。

除了基础设施监控之外，设置 JVM 指标和应用程序监控也很重要。

我所说的应用程序监控是指监控业务交易，如订单/交易数量、成功订单、失败订单、用户注册、触发的电子邮件等。

为此，我们必须从应用程序中发出事件。这些通常被称为业务指标。

Dropwizard metrics 是一个标准库，它允许你发出事件。

这些事件可以被发送到各种数据存储，最流行的是 [Graphite](https://metrics.dropwizard.io/4.0.0/manual/graphite.html) 和 [Influxdb](https://github.com/kickstarter/dropwizard-influxdb-reporter)

最受欢迎的指标类型有:

*   米-米测量一段时间内事件的速率。也称为每秒请求数或 TPS(每秒事务数)。米也跟踪 1 分钟、5 分钟和 15 分钟的均线，也称为`m1_rate`、`m5_rate`和`m15_rate`。仪表有`mark`方法来指示事件。

*   计数器-计数器用于跟踪计数。例如待定作业和总请求。Total requests 等通常是一个无用的参数，但是 graphite / influxdb 等工具可以在计数器上提供导数函数，这可以为我们提供使用计数器的仪表的附加功能。

*   计时器——计时器测量特定代码段被调用的速率以及其持续时间的分布。通常如果你使用定时器，你不需要计量器或者计数器，因为那些功能也是由定时器实现的。一个好的实践是为您的正常执行设置计时器，为异常场景设置计数器/计量器，这样您就可以直观地看到错误数量或错误率。计时器还使用变量`p50`、`p75`、`p95`、`p98`、`p99`和标准偏差来提供百分位数时间。

当使用 [Spring 框架](https://spring.io/)时， [Spring metrics](https://github.com/ryantenney/metrics-spring) 提供了与的紧密集成

值得注意的是， [metrics-spring](http://metrics.ryantenney.com/) 与 [spring metrics](https://docs.spring.io/spring-metrics/docs/current/public/prometheus) 是不同的库，后者是 Spring 框架本身的一部分。

下面的代码演示了一种简单的方法来引导 spring 应用程序，使用[drowizard 指标](https://metrics.dropwizard.io/4.0.0/)和[指标-spring](http://metrics.ryantenney.com/)
进行监控

```
buildscript {
    ext {
    }
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath 'org.springframework.boot:spring-boot-gradle-plugin:1.5.16.RELEASE'
    }
}

group 'metrics-example'
version '1.0-SNAPSHOT'

apply plugin: 'java'
apply plugin: 'org.springframework.boot'

sourceCompatibility = 1.8

repositories {
    mavenCentral()
}

dependencies {
    compile("org.springframework.boot:spring-boot-starter-web")
    compile 'org.projectlombok:lombok:1.16.12'
    compile group: 'io.dropwizard.metrics', name: 'metrics-core', version: '4.1.0'
    compile group: 'io.dropwizard.metrics', name: 'metrics-graphite', version: '4.1.0'
    compile group: 'com.ryantenney.metrics', name: 'metrics-spring', version: '3.1.3'
    compile group: 'com.codahale.metrics', name: 'metrics-jvm', version: '3.0.2'
    testCompile group: 'junit', name: 'junit', version: '4.12'
} 
```

```
package app;

import com.codahale.metrics.ConsoleReporter;
import com.codahale.metrics.Counter;
import com.codahale.metrics.JmxReporter;
import com.codahale.metrics.Meter;
import com.codahale.metrics.MetricFilter;
import com.codahale.metrics.MetricRegistry;
import com.codahale.metrics.graphite.Graphite;
import com.codahale.metrics.graphite.GraphiteReporter;
import com.codahale.metrics.jvm.GarbageCollectorMetricSet;
import com.codahale.metrics.jvm.MemoryUsageGaugeSet;
import com.codahale.metrics.jvm.ThreadStatesGaugeSet;
import com.ryantenney.metrics.spring.config.annotation.MetricsConfigurerAdapter;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

import javax.annotation.PostConstruct;
import java.net.InetSocketAddress;
import java.util.concurrent.TimeUnit;

@Component
public abstract class MetricsConfig extends MetricsConfigurerAdapter{

    private final MetricRegistry metrics = new MetricRegistry();
    Meter publishMeter = metrics.meter("publish.meter");
    Counter publishCounter = metrics.counter("publish.counter");

    @Value("${graphite.host}")
    private String graphiteHost;

    @Value("${graphite.port}")
    private int graphitePort;

    @Value("${graphite.amount.of.time.between.polls}")
    private long graphiteAmountOfTimeBetweenPolls;

    private String graphitePrefix;

    @PostConstruct
    public void run() {
        ConsoleReporter reporter = ConsoleReporter.forRegistry(metrics)
                .convertRatesTo(TimeUnit.SECONDS)
                .convertDurationsTo(TimeUnit.MILLISECONDS)
                .build();
        reporter.start(1, TimeUnit.SECONDS);

    }

    abstract protected void configureReporters();

    protected void configureReporters(String graphitePrefix) {
        this.graphitePrefix = graphitePrefix;
        configureReporters(metrics);
    }

    @Override
    public void configureReporters(MetricRegistry metricRegistry) {
        registerReporter(JmxReporter.forRegistry(metricRegistry)
                .build()).start();
        GraphiteReporter graphiteReporter =
                getGraphiteReporterBuilder(metricRegistry)
                        .build(getGraphite());
        registerReporter(graphiteReporter);
        graphiteReporter.start(graphiteAmountOfTimeBetweenPolls,
                TimeUnit.MILLISECONDS);
    }

    private GraphiteReporter.Builder getGraphiteReporterBuilder(MetricRegistry
                                                       metricRegistry) {
        metricRegistry.register("gc", new GarbageCollectorMetricSet());
        metricRegistry.register("memory", new MemoryUsageGaugeSet());
        metricRegistry.register("threads", new ThreadStatesGaugeSet());
        return GraphiteReporter.forRegistry(metricRegistry)
                .convertRatesTo(TimeUnit.SECONDS)
                .convertDurationsTo(TimeUnit.MILLISECONDS)
                .filter(MetricFilter.ALL)
                .prefixedWith(graphitePrefix);
    }

    private Graphite getGraphite() {
        return new Graphite(new InetSocketAddress(graphiteHost,
                graphitePort));
    }
} 
```

```
package app;

import org.springframework.stereotype.Component;

import javax.annotation.PostConstruct;

@Component
public class ProdMetricsConfig extends MetricsConfig {

    private static final String GRAPHITE_PREFIX =
            "collectd/graphite-monitoring-example/production";

    @Override
    protected void configureReporters() {
        configureReporters(GRAPHITE_PREFIX);
    }

    @PostConstruct()
    public void init() {
        configureReporters();
    }
} 
```

这是一个在 [Grafana](https://grafana.com/) 中可视化的仪表板示例

[![](img/8656b52e5fe710176081efaee9b916cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ePx9EOdq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/monitoring-example.png)

以上完整的项目可以在我的 [github 库](https://github.com/madhur/jvm-monitoring-example)中找到

repo 包含一个 dockerized spring boot 应用程序、grafana 和一个 graphite 实例。

假设你已经安装了 docker，只需执行下面的命令就可以启动并运行这个项目。

```
./gradlew build
docker build -t metrics-example .
docker-compose up -d 
```

使用`http://localhost:3000`浏览 grafana 门户网站并设置您的仪表盘。

如果你有任何建议/反馈，请告诉我