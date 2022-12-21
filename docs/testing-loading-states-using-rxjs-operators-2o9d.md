# 使用 RxJS 运算符测试加载状态

> 原文：<https://dev.to/bitovi/testing-loading-states-using-rxjs-operators-2o9d>

一个非常常见的模式是在获取数据时显示某种装载视图。在 Angular 中，我们可以使用 RxJS 的反应式编程方法来优雅地构建它——但是我们如何测试它呢？

假设我们从一个服务中获取一个猫名列表，并希望在发出请求时处理加载行为。我们可能会这样做:

```
import { Observable } from 'rxjs';
import { map, startWith } from 'rxjs/operators';

interface ResponseData<T> {
  data: Array<T>;
}
interface MappedData<T> {
  value: Array<T>;
  isLoading: boolean;
}

@Component({
  selector: 'cat-list',
  template: `
     <ng-container *ngIf="cats$ | async as cats">
        <div class="pending" *ngIf="cats?.isLoading; else loaded"></div>
        <ng-template #loaded>
            <div class="cat" *ngFor="let cat of cats.value">
            <p>Name: {{cat.name}}</p>
            </div>
        </ng-template>
    </ng-container>
`,
  styleUrls: ['./cat.component.less']
})
export class CatListComponent implements OnInit {
  public cats$: Observable<MappedData<Cat>>;

  constructor(private catService: CatService) { }

  ngOnInit() {
    this.cats$ = this.catService.getCats().pipe(
     map((res: ResponseData<Cat>) => {
      return {
        value: res.data,
        isLoading: false
      }
     }),
     startWith({
       value: [],
       isLoading: true
     })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们使用`startWith`操作符来设置我们的可观察对象，最初有一个空数组，并且加载值为真。在我们的单元测试中，我们将确保我们的 UI 反映了我们所期望的加载状态:

```
import { async, ComponentFixture, TestBed, fakeAsync, tick } from '@angular/core/testing';

import { CatListComponent } from './cat-list.component';
import { of, asyncScheduler } from 'rxjs';
import { CatsService } from '../catList/cats.service';

class MockCatsService {
  getCats() {
    return of({
      data: [{
        name: 'Sake',
        age: 10
      },
      {
        name: 'Butter',
        age: 15
      },
      {
        name: 'Parker',
        age: 7
      },
      {
        name: 'Kaylee',
        age: 2
      }]
    }, asyncScheduler);
  }
}

describe('CatListComponent', () => {
  let component: CatListComponent;
  let fixture: ComponentFixture<CatListComponent>;

  beforeEach(async(() => {
    TestBed.configureTestingModule({
      declarations: [ CatListComponent ],
      providers: [{
        provide: CatsService,
        useClass: MockCatsService
      }],
    })
    .compileComponents();
  }));

  it('should create', () => {
    const fixture = TestBed.createComponent(CatListComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
    expect(component).toBeTruthy();
    fixture.destroy();
  });

  it('should show loading div while results are loading', fakeAsync((): void => {
    const fixture = TestBed.createComponent(CatListComponent);
    fixture.detectChanges();
    const compiled = fixture.debugElement.nativeElement;
    const loadingDiv = compiled.querySelector('.loading');
    expect(loadingDiv).toBeTruthy();
    fixture.destroy();
  }));

  it('should show cat divs when results have loaded', fakeAsync((): void => {
    const fixture = TestBed.createComponent(CatListComponent);
    fixture.detectChanges();
    tick();
    fixture.detectChanges();
    tick();
    fixture.detectChanges();
    const compiled = fixture.debugElement.nativeElement;
    const loadingDiv = compiled.getElementsByClassName('cat');
    expect(loadingDiv.length).toBe(4);
    fixture.destroy();
  }));
}); 
```

Enter fullscreen mode Exit fullscreen mode

因为我想首先测试`isLoading`状态，我想能够在我的`getCats`方法之前看到 UI 的样子，所以我把我的断言包装在一个`fakeAsync`函数中。这个函数创建了一个假的异步区域，在这里我可以调用一个`tick`函数来模拟时间的流逝。通过这样做，我基本上可以测试我的可观测量，就好像它们是同步的一样。

我为每个“定时器”调用`tick`和`fixture.detectChanges`；像`ngOnInit`一样触发组件生命周期，当可观察对象被创建时，当可观察对象在视图中使用异步管道被订阅时，等等。