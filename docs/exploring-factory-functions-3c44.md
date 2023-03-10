# 探索工厂功能

> 原文：<https://dev.to/vicradon/exploring-factory-functions-3c44>

看着这些单词，**工厂**和**函数**会描绘出一幅创建对象的函数的图画。这正是工厂函数所做的。它们是创造物体的蓝图。更像是上课。但更温和，没有句法糖。

工厂函数允许通过调用带有一些参数的函数来快速创建对象。假设我们想要一个模拟人生工厂。我们需要一个函数，它输出具有以下属性的 sim:

1.  名字
2.  年龄
3.  性别

## 工厂

```
const makeSim = (name, age, gender) => {
  return {
    name, 
    age,
    gender
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

该函数只是返回一个对象。现在我们已经准备好了，我们可以向这个对象添加方法。

```
const makeSim = (name, age, gender) => {
  return {
    _name:name, 
    _age:age,
    _gender:gender,
    _occupation:occupation,
    _interests:interests, 
    get name() {return this._name},
    get age() {return this._age},
    get gender() {return this._gender}
  }
};
const Sam = makeSim('Samuel', 23, 'male');
console.log(Sam);    // outputs {name: "Sam", age: 23, gender: "male"}
console.log(Sam.name);    //outputs Samuel 
```

Enter fullscreen mode Exit fullscreen mode

所以这就是我对工厂功能的一点看法。在评论区发表你对这个话题的看法。谢了。