# 如何以角度显示物体的可观察性

> 原文：<https://dev.to/isamrish/how-to-display-observable-of-an-object-in-angular-22em>

使用异步管道很容易显示可观测的角度。

举个例子，

如果你想以角度显示一个可观察的字符串，它会像这样= >

演示.组件. ts

```
import { Component, OnInit } from '@angular/core';
import { Observable, of } from 'rxjs';

@Component({
  selector: 'app-demo',
  templateUrl: './demo.component.html',
  styleUrls: ['./demo.component.scss']
})
export class DemoComponent implements OnInit {
  astring$: Observable<string>;

  constructor() { }

  ngOnInit() {
    this.astring$ = of("Hello Observable!!!")
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

demo.component.html

```
 <p *ngIf="astring$">
    {{ astring$ | async }}
  </p> 
```

Enter fullscreen mode Exit fullscreen mode

#### 结果

你好可观察！！！

## 显示一个可观察的物体

同理，我们也可以显示物体的可观测性。

演示.组件. ts

```
import { Component, OnInit } from '@angular/core';
import { Observable, of } from 'rxjs';

export interface Person {
  name: string;
  place: string;
}

@Component({
  selector: 'app-demo',
  templateUrl: './demo.component.html',
  styleUrls: ['./demo.component.scss']
})
export class DemoComponent implements OnInit {

  myself$: Observable<Person>;

  constructor() { }

  ngOnInit() {

    this.myself$ = of({
      name: 'Amrish',
      place: 'Bangalore'
    })
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

演示.组件. ts

```
<div *ngIf="myself$">
  <p>Name: <span>{{ (myself$ | async).name }}</span></p>
  <p>Place: <span>{{ (myself$ | async).place }}</span></p>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

导致

姓名:阿姆里什
地点:班加罗尔

* * *

最有效的展示方式是

演示.组件. ts

```
<div>
  <div *ngIf="myself$ | async as myself">
    <p>Name: <span>{{ myself.name }}</span></p>
    <p>Place: <span>{{ myself.place }}</span></p>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode