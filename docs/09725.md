# 我们为什么要使用抽象类？

> 原文：<https://dev.to/realvino/why-do-we-use-abstract-class-4o1a>

抽象类和接口差异

抽象类接口
我们不能创建这个类的实例。接口只能是变量类型，不能是实例。它可以有构造函数。它不能有构造函数。
它可以派生出一些其他的类。它被创建为由其他类派生。
它可以实现(非抽象)一个或多个方法。它不能有函数定义。
具体类只能实现一个抽象类具体类可以实现许多接口
它可以包含也可以不包含抽象方法，它应该只有方法签名。它可以有私有的、受保护的内部数据成员。默认情况下，所有成员都是公共的。
它不能派生到结构。它可以由结构派生。
抽象类的实际实现

让我们来谈谈抽象类的实际实现。大多数人非常清楚这些类的理论，但是就实现而言，他们并不确定。

抽象类实现
公共抽象类 DataSource

{

受保护字符串 dataSourceName

私有字符串环境；

受保护数据源(string environment，string ds name){

this . environment = environment；

this . data source name = ds name；

GetDataSourceCredentials()；

}

private void GetDataSourceCredentials(){

控制台。WriteLine(字符串。Format("从配置文件中获取{1}环境的{0}的连接设置"，dataSourceName，environment))；

}

公共抽象 void OpenAndReturnConnection()；

}

public class mssqldata source:data source {

public mssqldata source(string environment):base(environment，" MsSQL "){ }

public override void openandreturconnection(){

Console。WriteLine(字符串。Format("为{0} dataSource 创建并返回连接"，data source name))；

}

}

公共类 Oracle data source:data source {

公共 Oracle data source(string environment):base(environment，" Oracle") {}

公共覆盖 void OpenAndReturnConnection(){

控制台。WriteLine(字符串。Format("为{0} dataSource 创建并返回连接"，data source name))；

}

}

我们应该知道一个抽象类可以有它对方法的实现。在上面给出的代码中，我创建了一个抽象基类，名为 DataSource。该类派生自具体的类，即 sSqlDataSource 和 OracleDataSource。混凝土类会有自己的方式来打开连接。应该有一种通用的方法来获取配置文件的连接字符串。

在我们的应用程序中，我们可能需要使用不同的数据源，比如 Ms SQL server、Oracle server 或者 MS Excel 文件。在上面提到的代码中，我有一个私有方法，用于根据数据源名称和环境(例如 DEV、QA 或 PROD)从配置文件中获取数据源连接字符串。

现在，如果您执行下面给出的代码:

data source sqlDS = new mssqldata source(" DEV ")；

sqlDS。OpenAndReturnConnection()；

我会得到以下输出:

这里，我正在获取开发环境的连接字符串。该功能对于从 DataSource 类派生的所有类都是通用的。连接的创建特定于派生类。因此，我们在抽象基类中有一个抽象方法。虽然这是一个非常基本的小例子，但是它可以帮助你理解抽象类的用法。