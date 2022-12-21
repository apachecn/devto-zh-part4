# 设计原则

> 原文：<https://dev.to/vikashagrawal/design-principles-4ajp>

# 固体

**单一责任原则**:一个类应该被定义为只有一个责任，以避免在该类混合了许多责任的情况下编译和测试完全依赖的变更。Logger 和 java Persistence API (JPA)是这方面的理想例子。
**开/关原理**:打开为扩展，关闭为修改。它要求定义接口，这样任何新的实现都可以独立地提供和插入，而无需修改现有的实现。

```
public class VehicleCalculations {
    public double calculateValue(Vehicle v) {
        if (v instanceof Car) {
            return v.getValue() * 0.8;
        if (v instanceof Bike) {
            return v.getValue() * 0.5;

    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果需要计算另一种车辆类型的成本，比如“自行车”,则需要修改上述方法。
但是，如果以如下方式修改上述类别，则可以单独添加任何新车型

```
public class Vehicle {
    public double calculateValue() {...}
}
public class Car extends Vehicle {
    public double calculateValue() {
        return this.getValue() * 0.8;
}
public class Cycle extends Vehicle{
    public double calculateValue() {
        return this.getValue() * 0.2;
} 
```

Enter fullscreen mode Exit fullscreen mode

Liskov 替换原则:使用基于接口的实现，你可以用子类方法的参数覆盖方法。意味着超类的类型可以用子类的对象替换。
**接口分离原则**:避免从接口实现不需要的方法。例如

```
public interface Vehicle {
    public void drive();
    public void stop();
    public void refuel();
    public void openDoors();
}
public class Bike implements Vehicle {

    // Can be implemented
    public void drive() {...}
    public void stop() {...}
    public void refuel() {...}

    // Can not be implemented
    public void openDoors() {...}
} 
```

Enter fullscreen mode Exit fullscreen mode

相反，它要求有另一个继承超接口的接口，并用特定的方法声明这个子接口。

```
public interface Vehicle {
    public void drive();
    public void stop();
    public void refuel();
}
public interface FourWheeler extends Vehicle {
    public void openDoors();
}
public class Bike implements Vehicle {
    public void drive() {...}
    public void stop() {...}
    public void refuel() {...}
} 
```

Enter fullscreen mode Exit fullscreen mode

**依赖倒置**:依赖应该是抽象的(接口和抽象类)，而不是具体的实现(类)。
考虑下面的例子。我们有一个汽车类，它依赖于具体的引擎类

```
public class Car {
    private Engine engine;
    public Car(Engine e) {
        engine = e;
    }
    public void start() {
        engine.start();
    }
}
public class Engine {
   public void start() {...}
} 
```

Enter fullscreen mode Exit fullscreen mode

代码暂时可以工作，但是如果我们想添加另一个引擎类型，比如说一个柴油引擎呢？这将需要重构汽车类。然而，我们可以通过引入一个抽象层来解决这个问题。与其让汽车直接依赖发动机，不如增加一个接口:

```
public interface EngineInterface {
    public void start();
}
public class Car {
    private EngineInterface engine;
    public Car(EngineInterface e) {
        engine = e;
    }
    public void start() {
        engine.start();
    }
}
public class PetrolEngine implements EngineInterface {
   public void start() {...}
}
public class DieselEngine implements EngineInterface {
   public void start() {...}
} 
```

Enter fullscreen mode Exit fullscreen mode

# 干

不要重复自己，以避免代码的重复，这样可维护性就变得容易了。这也有助于找到错误。

# 休息

统一接口:系统中的资源应该只有一个逻辑 URI，并且应该提供获取相关或附加数据的方法。客户端服务器:这实质上意味着客户端应用程序和服务器应用程序必须能够独立发展，而不相互依赖。
无状态:如果客户端应用程序需要成为最终用户的有状态应用程序，用户登录一次，然后进行其他授权操作，那么来自客户端的每个请求都应该包含服务请求所需的所有信息，包括身份验证和授权细节。
可缓存:管理良好的缓存部分或完全消除了一些客户端-服务器交互，进一步提高了可伸缩性和性能。
·层:REST 允许您使用分层的系统架构，在服务器 A 上部署 API，在服务器 B 上存储数据，在服务器 C 上验证请求
·按需编码(可选):您可以自由返回可执行代码以支持您的应用程序的一部分，例如，客户端可能会调用您的 API 来获取 UI 小部件渲染代码。

# 微服务

领域驱动设计:领域驱动设计以及相关的技术逻辑确保了单一责任微服务。隐藏实现细节:完整应用程序中包含如此多的微服务，隐藏一个微服务的实现。
分散式:由于有如此多的微服务，it 可以灵活地为每个微服务提供不同的数据库或同一数据库的不同模式。有了它，数据库迁移变得更加容易。
故障隔离:由于可伸缩性或性能影响特定功能而非整个应用程序的任何原因，一个微服务不可用。
持续交付:单个应用程序中包含如此多的微服务，必须拥有 CI/CD 管道，因此需要开发人员和开发运营人员之间的协作。

# 最佳实践

封装变更:将所有方法和字段声明为私有，然后根据需要增加可访问性，不要直接跳到公共。更喜欢组合而不是继承:组合允许在运行时通过设置属性来改变类的行为。
为接口而非实现编程:在变量、返回类型或方法的参数中使用接口类型。
授权原则:不要自己做所有的事情。比如，如果你想用 equals 或 hashcode 来比较任何对象，客户端不会这么做，而是由类来做。