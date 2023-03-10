# 如何延伸有角度的内置管道，为什么

> 原文：<https://dev.to/codewithahsan/how-to-extend-angular-built-in-pipes-and-why-1lp7>

*本文最初发表于[https://ahsanayaz.com/extend-angular-built-in-pipes](https://ahsanayaz.com/extend-angular-built-in-pipes)。*

Angular 提供了许多内置管道，通常足以满足我们日常的 angular 工作。然而，有时我们发现自己陷入困境，这可能需要我们写自己的解决方案。在这种情况下，我们可能会从头开始写一些东西。但是，为什么要重新发明轮子，而不是在 Angular 已经提供给我们的基础上构建呢？在本文中，我们将扩展一个角形管道来满足我们自己的需求。

我们将扩展 Angular 提供的可以在`@angular/common`包下找到的`titleCase`管道。参见[文档](https://angular.io/api/common/TitleCasePipe#usage-notes)。

首先，我们必须创建一个名为`titleCaseExtended`的管道，您可以简单地使用:
来完成

```
ng generate pipe path-to-folder/title-case-extended 
```

Enter fullscreen mode Exit fullscreen mode

上面应该会在文件夹`path-to-folder`下为你创建文件，命名为`title-case-extended.pipe.ts`。让我们看看下面文件的内容:

```
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'titleCaseExtended'
})
export class TitleCaseExtendedPipe implements PipeTransform {

  transform(value: any, ...args: any[]): any {
    return null;
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

## 扩展 Angular 的标题栏

我们将使用 Angular 内置的`titleCase`管道来扩展管道，如下所示:

```
import { Pipe, PipeTransform } from '@angular/core';
import { TitleCasePipe } from '@angular/common';

@Pipe({
  name: 'titleCaseExtended'
})
export class TitleCaseExtendedPipe extends TitleCasePipe implements PipeTransform {
  constructor () {
    super();
  }
  transform(value: any, ...args: any[]): any {
    return null;
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

## 编写自定义功能

现在我们已经扩展了管道，可以编写自己的逻辑了。Angular 的`titleCase`管道只接受一个*字符串*值，它可以将其转换为*标题大小写*。假设我们必须将一个对象数组(`[{}]`)作为输入传递给一个不属于我们的组件。也就是说，我们无法访问它的模板。我们不能将 Angular 的`titleCase`应用于数组中项目的属性，如果那是我们想要转换的。正是因为这个原因，我们创建了`titleCaseExtended`管道。

首先，我们将确保我们的管道也服务于`titleCase`的目的。也就是说，它也适用于一个简单的字符串:

```
import { Pipe, PipeTransform } from '@angular/core';
import { TitleCasePipe } from '@angular/common';

@Pipe({
  name: 'titleCaseExtended'
})
export class TitleCaseExtendedPipe extends TitleCasePipe implements PipeTransform {
  constructor () {
    super();
  }
  transform(value: any, ...args: any[]): any {
    if (typeof value === 'string') {
      // if the value we have to transform is a simple string
      return super.transform(value);
    }
    return null;
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

管道应用于管柱的使用情况见下面的用法:

```
<!-- user.name is a string value -->
<div>
  {{user.name | titleCaseExtended}}
</div> 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们将在处理数组时处理这种情况。为此，我们将简单地遍历数组并转换它的元素:

```
import { Pipe, PipeTransform } from '@angular/core';
import { TitleCasePipe } from '@angular/common';

@Pipe({
  name: 'titleCaseExtended'
})
export class TitleCaseExtendedPipe extends TitleCasePipe implements PipeTransform {
  constructor () {
    super();
  }
  transform(value: any, ...args: any[]): any {
    if (typeof value === 'string') {
      // if the value we have to transform is a simple string
      return super.transform(value);
    } else if (Array.isArray(value)) {
      // if the value we have to transform is an array
      return value.map((item) => {
        // transform item here..
        return item;
      });
    }
    return null;
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以看到两种可能性:

*   数组中的每个`item`都是一个简单的字符串。也就是说，我们必须转换`string[]`。
*   数组中的每个`item`都是一个对象，我们有一个`property`可以使用。也就是说，我们必须改造`item[property]`。

下面我们来处理这些案例。

### 转换字符串值数组

为了处理字符串数组，我们将使用 Angular 的`titleCase`管道简单地转换数组的每个元素。

```
import { Pipe, PipeTransform } from '@angular/core';
import { TitleCasePipe } from '@angular/common';

@Pipe({
  name: 'titleCaseExtended'
})
export class TitleCaseExtendedPipe extends TitleCasePipe implements PipeTransform {
  constructor () {
    super();
  }
  transform(value: any, ...args: any[]): any {
    if (typeof value === 'string') {
      // if the value we have to transform is a simple string
      return super.transform(value);
    } else if (Array.isArray(value)) {
      // if the value we have to transform is an array
      return value.map((item) => {
        // if the current item in the array is a simple string, we transform it
        if (typeof item === 'string') {
          return super.transform(item);
        }
        return item;
      });
    }
    return null;
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

下面是一个字符串数组的管道用法示例:

```
@Component({})
class MyComponent {
  subjects = ['Computer Science', 'Maths', 'Biology']
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<!-- each item in `subjects` is a string value -->
<div class="subjects">
  <div class="subjects__subject"
    *ngFor="let subject of subjects | titleCaseExtended">
    <!-- we don't have to modify the `name` property here now -->
    {{user.name}}
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

### 变换一组对象

要处理对象数组，我们必须知道对象内部的哪个属性要被转换。例如，如果我们有一个`users`数组，它有一个需要转换的属性`full_name`，我们需要以某种方式在管道中传递这个属性`full_name`。

首先，让我们添加代码来读取所需属性的参数:

```
import { Pipe, PipeTransform } from '@angular/core';
import { TitleCasePipe } from '@angular/common';

@Pipe({
  name: 'titleCaseExtended'
})
export class TitleCaseExtendedPipe extends TitleCasePipe implements PipeTransform {
  constructor () {
    super();
  }
  transform(value: any, ...args: any[]): any {
    const property = args[0];
    const isValidProperty = property && typeof property === 'string';
    if (typeof value === 'string') {
      // if the value we have to transform is a simple string
      return super.transform(value);
    } else if (Array.isArray(value)) {
      // if the value we have to transform is an array
      return value.map((item) => {
        // if the current item in the array is a simple string, we transform it
        if (typeof item === 'string') {
          return super.transform(item);
        }
        return item;
      });
    }
    return null;
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码片段中，我们使用`args[0]`读取了`transform`函数的第一个参数，并将其赋给了变量`property`。然后我们验证`property`，看看属性的类型是否是`string`，这样我们就可以转换它。

下一步是使用`property`并在每个`item`中转换它。请看下面的代码片段:

```
import { Pipe, PipeTransform } from '@angular/core';
import { TitleCasePipe } from '@angular/common';

@Pipe({
  name: 'titleCaseExtended'
})
export class TitleCaseExtendedPipe extends TitleCasePipe implements PipeTransform {
  constructor () {
    super();
  }
  transform(value: any, ...args: any[]): any {
    const property = args[0];
    const isValidProperty = property && typeof property === 'string';
    if (typeof value === 'string') {
      // if the value we have to transform is a simple string
      return super.transform(value);
    } else if (Array.isArray(value)) {
      // if the value we have to transform is an array
      return value.map((item) => {
        // if the current item in the array is a simple string, we transform it
        if (typeof item === 'string') {
          return super.transform(item);
        } else if (isValidProperty && item[property]) {
          // if the item in the array is an object and we have the property in the object, we transform item
          item[property] = super.transform(item[property]);
        }
        return item;
      });
    }
    return null;
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

下面是一个对象数组的管道使用示例，带有[ngx-bootstrap type ahead](https://ng-bootstrap.github.io/#/components/typeahead):

```
@Component({})
class MyComponent {
  users = [{
    full_name: 'Ahsan Ayaz'
  }, {
    full_name: 'Mohsin Ayaz'
  }, {
    full_name: 'Saad Qamar'
  }, {
    full_name: 'Mehdi Rajani'
  }]
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<!-- each item in `subjects` is a string value -->
<form class="form">
  <input class="owner"
    id="ownerInput" [typeahead]="users | titleCaseExtended : 'full_name'" type="text"
    typeaheadWaitMs="300" typeaheadOptionField="full_name">
</form> 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们使用了`| titleCaseExtended : 'full_name'`。这个`full_name`是由 Angular 传递给`transform`方法的字符串，然后我们使用`args[0]`将它放入我们的管道中。NgxBootstrap 的带有对象数组的 typeahead 是一个非常好的用例，我们的管道可以在这里大放异彩。因为在将属性作为输入传递给 typeahead 时，我们不能转换项目内部的属性。

Welp！我们的延伸管道现在可以使用了。您也可以从这个[要点](https://gist.github.com/AhsanAyaz/29325c3ecfec5949e7de2d56ac1c7678)中获得管道的代码和示例用法。

## 结论

Angular 提供了许多现成的东西，我们可以在这些东西的基础上构建自己的东西。这只是一个例子。读完这篇文章后，我真的很想知道你构建了什么。您可以扩展任何服务或管道来创建自己的:)

如果你在这篇文章中学到了新的东西，一定要在你的圈子里分享。

此外，当你去[https://ahsanayaz.com](https://ahsanayaz.com)或[订阅我的 Youtube 频道](https://www.youtube.com/channel/UCAys-Lg76QcRNGc0dOr_bXA?sub_confirmation=1)时，你会看到我这些天分享的关于 Angular、JavaScript &网络开发的更多精彩内容。