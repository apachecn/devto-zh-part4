# Flutter:一切都是小部件系列-第 3 部分:小部件中的状态。

> 原文：<https://dev.to/topeomot/flutter-everything-is-a-widget-series-part-3-state-in-widgets-lml>

在使用 Flutter 时，你很快会想到的一个主题是，一切都是小部件。本系列旨在帮助初学者理解这个简单而强大的概念，并向他们介绍 Flutter 中的基本小部件。

为了在这个旅程中帮助我们，我仅使用 Flutter 自带的内置小部件构建了一个 Twitter 移动克隆应用。你可以在 https://github.com/topeomot2/twitter-ui-app 找到代码

* * *

在讨论不同的小部件之前，我们需要了解“状态”的概念。这不是 Flutter 独有的术语，在其他语言中也可以找到，最著名的是 React。

状态通常是指一组属性，这些属性决定了应用程序所处的“情况/条件”。我见过的一个最好的类比，就是把温度比作决定水的“状况”的状态属性。把温度提高到 100⁰C 及以上，水变成蒸汽(气态)，低于 0⁰C，水变成冰(固态)。

通常，状态存在于应用程序的不同级别。状态管理是一个主要话题，可以在网上找到相关文章。这个[链接](https://app.egghead.io/articles/what-is-state-why-do-i-need-to-manage-it)包括对状态的更技术性的观察。

对于小部件级别的状态，Flutter 给了我们一个状态类。下面是该类的 Flutter 源代码中的注释。

“状态是指(1)在构建小部件时可以同步读取的信息，以及(2)在小部件的生命周期内可能会更改的信息。”

根据小部件是否有状态类，Flutter 中有两种基本的小部件类型，无状态小部件和有状态小部件。

### 无状态小工具

来自 Flutter 源代码

> StatelessWidgets 类用于在给定特定配置和环境状态的情况下始终以相同方式构建的小部件。

无状态小部件没有附加的状态类。小部件创建期间传递的任何配置都不能由小部件本身在内部进行更改。下面是一个无状态小部件的例子。

```
class StatelessSample extends StatelessWidget {

  final int count;

  const StatelessSample({Key key, this.count}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Container(
      child: Text(count.toString(),
        style: TextStyle(
          color: Colors.white
        ),
      ),
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   注意，在创建过程中，任何想要放入小部件的东西都必须通过构造函数进入，并且必须声明为 final。也就是说，如果你传入相同的配置，你将在你的小部件中得到相同的状态。

*   小部件通过在自己的构建方法中返回一个小部件来创建自己的 UI。

*   创建小部件后，小部件中的任何东西都不能直接更改它的 ui。只有通过外部传入另一个“计数”进行完全重新创建，才能改变正在显示的计数的值。

由于上述原因，无状态窗口小部件对于主要用于显示信息的用户界面部分是理想的，显然信息必须被传递到构造函数中。

### 有状态小部件

来自 Flutter 源代码

> StatefulWidget 是一个具有可变状态的小部件。

让我们看一个简单的例子。

```
class StatefulSample extends StatefulWidget {

  final int count;

  const StatefulSample({Key key, this.count}) : super(key: key);

  @override
  _StatefulSampleState createState() => _StatefulSampleState();
}

class _StatefulSampleState extends State<StatefulSample> {

  int count;

  @override
  void initState() {
    super.initState();
    count = widget.count;
  }
  @override
  Widget build(BuildContext context) {
    return Center(
      child: Column(
        children: <Widget>[
          Container(
            child: Text(count.toString(),
              style: TextStyle(
                  color: Colors.white
              ),
            ),
          ),
          RaisedButton(
            child: Text('Increase Count'),
            onPressed: (){
              setState(() {
                count += 1;
              });
            }
          )
        ],
      ),
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意以下几点

*   对于有状态小部件，有两个类，一个类扩展内置的 Flutter StatefulWidget 本身，另一个类扩展内置的 State 类。每个 StatefulWidget 都必须通过 createState 方法附加一个 State 类。

*   有状态小部件的实际 UI 来自附加到它的状态对象。您通过在状态的构建函数中返回一个小部件来指示 UI。

*   State 类有一个 setState 方法，Flutter 源代码说“通知框架这个对象的内部状态发生了变化”。这个函数通知框架，框架用状态的更新属性再次运行构建函数。因此，即使外部的“计数”不能被改变，因为它是最终的，我们可以更新 UI，因为我们正在使用状态对象的内部计数变量，并且我们正在通过调用 setState()来重建 UI；

*   iniState 是一个在任何状态对象的初始化过程中调用一次的方法。我们从这里设置内部计数变量的初始值。

上面的属性使得有状态窗口小部件成为控制其他窗口小部件显示的容器窗口小部件的理想选择。

* * *

> 无论您使用哪种方法，无状态或有状态小部件中的 build 方法都是您将返回想要显示的内容的地方。正如我们在上节课中所学的，这可以是简单的内置部件，也可以是复杂的组合。