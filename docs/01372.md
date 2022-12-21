# 通过标记向模具传递对象或数组

> 原文：<https://dev.to/teamhive/passing-an-object-or-array-to-stencil-through-markup-5657>

## 常用代码

```
export interface SomeValue {
  name: string;
  value: string;
} 
```

## 路过一个物体

```
import { Component, h, Prop, Watch } from '@stencil/core';
import { SomeValue } from './some-value.interface';
@Component({
  tag: 'object-sample',
  styleUrl: '...',
  shadow: true
})
export class ObjectSample {
  private _objectData: SomeValue;
  @Prop() objectData: SomeValue | string;

  @Watch('objectData')
  objectDataWatcher(newValue: SomeValue | string) {
    if (typeof newValue === 'string') {
      this._objectData = JSON.parse(newValue);
    }
    else {
      this._objectData = newValue;
    }
  }

  componentWillLoad() {
    this.objectDataWatcher(this.objectData);
  }

  render() {
    return (
      <div class='object-data'>
        <span>{this._objectData.name}</span>
        <span>{this._objectData.value}</span>
      </div>
    );
  }
} 
```

```
<object-sample 
   object-data='{"name": "Bird", "value": "Chirp"}'>
</object-sample> 
```

## 传递一个数组

```
import { Component, h, Prop, Watch } from '@stencil/core';
import { SomeValue } from './some-value.interface';
@Component({
  tag: 'array-sample',
  styleUrl: '...',
  shadow: true
})
export class ArraySample {

  private _arrayData: SomeValue[];
  @Prop() arrayData: SomeValue[] | string;

  @Watch('arrayData')
  arrayDataWatcher(newValue: SomeValue[] | string) {
    if (typeof newValue === 'string') {
       this._arrayData = JSON.parse(newValue);
    }
    else {
      this._arrayData = newValue;
    }
  }

  componentWillLoad() {
    this.arrayDataWatcher(this.arrayData);
  }

  render() {
    return [
      <div class='array-size'>{ this._arrayData.length }</div>,
      this._arrayData.map(x =>
        <div class='row'>
          <span>{x.name}</span>
          <span>{x.value}</span>
        </div>
      )
    ];
  }
} 
```

```
<array-sample 
   array-data='[{"name": "Cat", "value": "Meow"}, {"name": "Dog", "value": "Woof"}]'>
</array-sample> 
```

## 关键概念

*   如果定义了类型，`@Prop()`数据类型必须包含字符串
*   使用一个`@Watch()`函数来解析 json 字符串值
*   调用`componentWillLoad()`函数中的`@Watch()`函数对 json 数据进行第一次解析
    *   组件第一次加载时,`@Watch()`功能不会触发
*   传递给`@Prop()`的值必须遵循严格的 json 语法

## 版本考虑

这些例子是模板一。如果您正在使用 pre @stencil/core@one，请查看 Breaking changes 文档，以了解需要哪些更新来让此示例在测试版中为您工作