# 你最引以为豪的 RxJs/ NgRx 码位是什么？

> 原文：<https://dev.to/johannesjo/what-s-the-rxjs-ngrx-code-bit-you-are-most-proud-of-97c>

RxJs 允许你只用几行代码就能做强大的事情，这有时会感觉像是解决一个非常复杂的难题，尤其是当你对 observables 这个世界还是个新手的时候。你最自豪的一点是什么？

我从一个简单的计时器开始(可能不太令人印象深刻，但有 1000 种改进的方法)，这个计时器用于在短时间后隐藏一个弹出窗口:

```
export class PopupComponent {
    private _triggerPopup$ = new Subject<any>();

    isShowPopup$: Observable<boolean> = this._triggerPopup$.pipe(
        switchMap((): Observable<boolean> => merge(
            of(true),
            timer(POPUP_TIMEOUT_DURATION).pipe(mapTo(false))
        )),
    );

} 
```