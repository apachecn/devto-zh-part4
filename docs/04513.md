# ECMAScript 6 中的 Mixins 笨拙、不方便、强大。

> 原文：<https://dev.to/codelitically_incorrect/mixins-in-ecmascript-6-awkward-inconvenient-powerful-4jf2>

(更新:我真的不确定 ECMAScript 中的“mixins”提案的状态。以下信息是我在 2017 年从另一个来源获得的片段。我确实知道我们能够通过 Object.assign(..)但是我希望它能成为未来 ES 的类定义语法的一部分。)

* * *

那么你已经听说 ECMAScript 6 将支持“Mixins”了。一些语言将这一特性实现为“特征”或共享模块。这个概念的起源，我感觉，诞生于一种叫做 SELF 的语言(一个太阳微系统的实验，见:[http://www.selflanguage.org/](http://www.selflanguage.org/))。

简单地说，traits 或 mixins 是一种语言特性，它允许对象类共享一组我们称之为方法或函数，甚至可能是数据属性的公共行为。不是继承，有人认为是多重继承。当继承没有意义时，它是简单代码重用的任意实现。

例如:在构建一个游戏时，角色、等级或武器类可能都拥有将消息记录到控制台以供调试的能力，因此一个名为 Loggable 的 mixin 可以用来打包一些方法，以便在类层次结构中共享。这样，即使不相关的类也可能共享这个共同的特征。如您所见，继承在逻辑上或语义上都没有意义。一个人不是可记录的。它听起来不对，读起来不对，它与代码的美丽和清晰相冲突。

下面是 Ruby 表示。它是一个美人。

```
module Loggable
   def log (msg)
      puts msg
   end
end

class Character include Loggable
   def jump
      log("jumped")
   end
end

class Sword < Weapon  include Loggable
   def animate_blade
      log("do sword animation");
   end
end 
```

在 Scala 中，这是一种美:

```
class Sword extends Weapon with Loggable {
   def animate_blade() { 
      log("do sword animation")
   }
} 
```

在 ECMAScript 6 中:

```
var Loggable = Base => class extends Base {
  log(s) { }
};
class Weapon { }
class Sword extends Loggable(Weapon) { } 
```

WTF？！！！！！
如果你想要 2 个或更多混音，情况会变得很糟糕:

```
class Weapon { }
class Sword extends Loggable(Localizable(Weapon)) { } 
```

我不知道。很强大。绝对不是美女！