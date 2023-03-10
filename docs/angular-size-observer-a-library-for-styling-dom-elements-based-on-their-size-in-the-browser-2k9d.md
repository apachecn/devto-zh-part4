# Angular Size Observer:一个基于浏览器中 DOM 元素的大小来设计它们样式的库。

> 原文：<https://dev.to/johncarroll/angular-size-observer-a-library-for-styling-dom-elements-based-on-their-size-in-the-browser-2k9d>

[Angular Size Observer](https://gitlab.com/service-work/size-observer) 包括 Angular 应用程序的工具，用于监控浏览器中元素的显示大小并做出反应。想想 CSS 媒体查询，除了基于 DOM 元素大小而不是浏览器屏幕大小应用 CSS(顺便说一下:参见 [CSS 元素查询规范提案](https://github.com/tomhodgins/element-queries-spec))。

该库的主要组件是一个`SWObserveSize`指令。在最基本的情况下，只需将指令应用于 DOM 元素，该指令将根据元素的显示宽度自动应用一个特殊的 CSS 类，并根据元素的显示高度应用另一个 CSS 类。

示例:

```
@Component({
  selector: 'app-root',
  template: `
    <div id="the-div" swObserveSize>
      <!-- div content... -->
    </div>
  `,
})
export class AppComponent {} 
```

让我们假设在上面的例子中，`'the-div'`的显示尺寸是`1250` px 宽，`650` px 高。SWObserveSize 指令将对`'the-div'`元素应用两个 CSS 类:

*   `'sw-container-width-xlarge'`。
*   `'sw-container-height-small'`。

当`'the-div'`在浏览器中的显示尺寸发生变化时，SWObserveSize 会自动更新`'the-div'`的 CSS 类。

通过提供可选的`SW_SIZE_OBSERVER_CONFIG`配置标记，您可以定制 SWObserveSize 应用于元素的 CSS 类，以及与这些类相关联的宽度/高度断点。

如果您什么都不做，将自动使用的默认配置标记:

```
const defaultSizeObserverConfig: ISWSizeObserverConfig = {
  defaultWidthClass: 'sw-container-width-xtiny',
  defaultHeightClass: 'sw-container-height-xtiny',
  widthBreakpoints: new Map([
    [1200, 'sw-container-width-xlarge'],
    [1024, 'sw-container-width-large'],
    [768, 'sw-container-width-medium'],
    [600, 'sw-container-width-small'],
    [500, 'sw-container-width-xsmall'],
    [250, 'sw-container-width-tiny'],
  ]),
  heightBreakpoints: new Map([
    [1200, 'sw-container-height-xlarge'],
    [1024, 'sw-container-height-large'],
    [768, 'sw-container-height-medium'],
    [600, 'sw-container-height-small'],
    [500, 'sw-container-height-xsmall'],
    [250, 'sw-container-height-tiny'],
  ]),
}; 
```

这个配置解释为，“如果一个元素的显示宽度是……”

1.  `>= 1200` px，应用`'sw-container-width-xlarge'` CSS 类。
2.  `>= 1024` px，应用`'sw-container-width-large'` CSS 类。
3.  `>= 768` px，应用`'sw-container-width-medium'` CSS 类。
4.  等等...
5.  否则，应用`'sw-container-width-xtiny'` CSS 类。

同样的逻辑也适用于显示元素的高度。

## 了解更多

你可以在 GitLab 的报告中找到更多关于 Angular Size Observer 库的信息，包括更高级的用法`SizeObserverService`:[https://gitlab.com/service-work/size-observer](https://gitlab.com/service-work/size-observer)