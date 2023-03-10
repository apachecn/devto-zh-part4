# Angular & Apollo 入门

> 原文：<https://dev.to/thisdotmedia/getting-started-with-angular-apollo-1dd5>

GraphQL 开始解决 RESTful API 架构存在的一些问题。类似这样的问题:过量提取(获得的数据远远超过我的需要)、欠取(没有获得足够的数据，不得不进行另一次往返)、全有或全无(如果在试图检索任何数据时发生错误，操作失败)、类型安全(需要预期的，知道将返回什么；因此，更少的错误)等。这是一个非常强大的规范(向强大的解析器致敬),已经在行业中引起了相当大的转变。

本文的重点将是通过 Angular SPA 与 GraphQL API 交互来查询和变更数据。我们将围绕活动日历建立一个应用程序，查看活动，创建和编辑活动等。为了连接到我们的 GraphQL API，我们将使用 [Apollo Angular](https://www.apollographql.com/docs/angular/) 库。Apollo 是 GraphQL 的一个很好的平台实现，有各种很棒的库、工具集，甚至还有一个 GraphQL 服务器。

在这里跟随[回购](https://github.com/cmwhited/event-calendar)。

## 设置

首先，让我们使用 [angular cli](https://cli.angular.io/) 创建一个新的 Angular 应用程序。如果你还没有安装 angular cli，很简单，打开你喜欢的终端，用 npm 全球安装:

```
npm i -g @angular/cli 
```

完成后，您可以通过检查版本来验证它是否安装成功:

```
ng --version 
```

### 创建 App

现在，让我们使用 cli 创建应用程序(您将希望位于应用程序的安装目录，如果是这样，请先进入该目录):

```
$ ng new event-calendar --style=scss --routing=true 
```

还有万岁！一款新的 angular app！为了理智起见，让我们确保一切顺利，`cd`进入新的应用程序目录并运行它:

```
$ cd event-calendar
$ ng serve 
```

如果应用程序成功启动，您应该能够打开网络浏览器窗口，导航到 [http://localhost:4200/](http://localhost:4200/) 并查看应用程序。注意。4200 是默认端口，如果您想在其他端口上运行它，将该端口作为参数添加到`ng serve`命令:

```
$ ng serve --port 4201 
```

### 添加阿波罗

cli 的`ng add`功能使得向我们的 angular 应用添加新模块变得非常容易。包括 angular apollo GraphQL 客户端实现模块；

```
$ ng add apollo-angular 
```

这个命令为我们做了几件事:

*   将所需的依赖项安装并添加到我们的`package.json`
*   创建一个 GraphQL 模块:`./src/app/graphql.module.ts`，它具有创建到我们的 graphql 服务器的连接所需的初始设置，并将该连接公开为一个 angular 模块
*   将 GraphQL 模块添加到应用程序模块:`./src/app/app.module`导入属性，使其可用于我们的应用程序。

这太棒了，因为它为我们处理了最初的样板工作。我们唯一需要做的就是设置 GraphQL API 服务器运行的 URI。打开`./src/app/graphql.module.ts` GraphQL 模块文件，您将看到一个名为`uri`的`const`变量，它是一个空字符串，带有一个有用的注释，告诉您“< -在此添加 GraphQL 服务器的 URL”。继续按照评论所说的做，让我们添加我们的 GraphQL 服务器 URI。为了本文的目的，URI 将是:`http://127.0.0.1:3000/graphql`。注意这是我运行的一个本地 graphql api，查看 [repo](https://github.com/cmwhited/event-calendar-api) 了解更多信息。

### 添加棱角分明的材质造型

这篇文章的重点不是角材料，但它是这个项目的一个组成部分。检查[角材料文件](https://material.angular.io)的用法、组件文件、指南等。我们将使用`ng add`将角度材质模块添加到我们的应用程序中，就像我们对阿波罗所做的一样:

```
$ ng add @angular/material 
```

这将提示您一些问题:

1.  选择一个预构建的主题名称，或者为自定义主题选择“自定义”→我选择了深紫色/琥珀色。选择你喜欢的任何东西。
2.  为手势识别设置 HammerJS
3.  为角度材质设置浏览器动画→ Y

就像 apollo 一样，这将安装所需的依赖项并更新`package.json`。它还添加了主题信息到`./src/styles.scss`以及导入机器人到&材质设计图标字体。

## 获取日历事件

这是足够的样板/设置。是时候开始利用 GraphQL 的力量了。让我们从一个查询开始，获取日历的事件列表并显示这些事件。

### 创建日历-事件模块

棱角分明的建筑促进了`modules`的产生；按功能考虑包装。一个模块应该包含提供完整功能的所有必要组件。比如:

*   按指定路线发送
*   模型
*   服务
*   路线守卫
*   成分

这些都是传统的角类类型，我们还会有:

*   问题
*   突变

为了支持这个模块与我们的 GraphQL 服务器交互来查询和修改数据。同样，我们将使用 cli 创建我们的模块:

```
$ ng g module calendar-event --routing=true 
```

这为我们创建了一个名为`./src/app/calendar-event`的目录，其中有两个文件:`./src/app/calendar-event/calendar-event-routing.module.ts`和`./src/app/calendar-event/calendar-event.module.ts`。这些是我们模块的构建模块。我们可以暂时不去管这些。

### 日历事件模型

让我们创建一个表示日历事件的模型。在`./src/app/calendar-event`中创建一个名为`models`的目录。并在这个目录下创建一个文件:`calendar-event.model.ts`。我们将在这里定义代表日历事件条目的模型。

```
export type EventStatus = ‘UPCOMING’ | ‘STARTED’ | ‘COMPLETED’;
export type AttendingStatus = ‘GOING’ | ‘PENDING’ | ‘NOT_GOING’;

export type Guest = {
    _id: string;
    name: string;
    email: string;
    attending: AttendingStatus;
}

export type CalendarEvent = {
    _id: string;
    Status: EventStatus;
    eventStart: string;
    startTime: string;
    eventEnd: string;
    endTime: string;
    name: string;
    description?: string;
    location?: string;
    guests?: Guest[];
} 
```

### 图形 SQL 愿望

为了解决 REST 的上/下取问题，GraphQL 作为 API 的查询框架而存在。这意味着，作为 API 的客户/消费者，您可以定义希望从 API 返回的字段。这是难以置信的强大。它允许我们只选择我们*想要/需要*返回的字段，而没有所有字段的潜在开销。

另一方面，不存在潜在的第二次往返请求。例如，如果你有一个对象列表，而你的列表中只需要 id 和几个字段，那么这就是你的查询，这就是 API 返回的结果；不需要的字段不会造成额外的膨胀。然后，如果用户导航到详细信息页面，您可以运行另一个查询来返回对象中的所有字段并显示它们。我们还可以在一个请求中指定多个查询，它将返回所有查询的结果。

*注意*它不一定按照您给的顺序处理查询。

让我们看一个简单的例子。

对于这个例子，我们有一个 API，它公开了两个查询:`hello`，它返回一个`string`:‘HELLO’，和`world`，它也返回一个`string`:‘WORLD’。为了检索这两者，我们可以编写一个查询:

```
query RetrieveHelloWorld {
    hello
    world
} 
```

提交后，它将运行这两个查询，并在响应中返回这两个查询:

```
{
    “data”: {
        “hello”: “HELLO”,
        “world”: “WORLD”
    }
} 
```

厉害！查询和数据都返回。这太神奇了。想象一下，在加载应用程序时，您可以获得经过身份验证的用户和您想要的任何初始数据，而不是必须分别发出每个请求，与服务器进行多次往返。

### 在 Apollo Angular 中添加查询

Apollo 提供了几种方法来查询 Angular 中的数据。现在就让我们来探索一下。

运行查询的第一种方式是利用由`apollo-angular`库提供的`Apollo`服务。就像任何服务提供者一样，在组件的构造函数中注入`Apollo`服务，并将 graphql 查询传递给`Apollo`服务上的`.watchQuery`方法。这个方法的`valueChanges`返回我们可以与之交互的数据的`Observable`。让我们创建一个组件来查询一列`CalendarEvent`记录:

```
//src/app/calendar-event/containers/calendar-events-list-container/calendar-events-list-container.component.ts
import { Component, OnInit } from '@angular/core';
import { Apollo } from 'apollo-angular';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';
import gql from 'graphql-tag';

import { CalendarEvent } from '../../models;

const calendarEventsListQuery = gql`
    query CalendarEventsQuery {
        events {
            _id
            name
            description
            eventStart
            startTime
            eventEnd
            endTime
        }
    }
`;

@Component({
    selector: ‘app-calendar-events-list-container’,
    templateUrl: ‘./calendar-events-list-container.component.html’,
    styleUrls: [‘./calendar-events-list-container.component.scss’]
})
export class CalendarEventsListContainerComponent implements OnInit {
    calendarEvents$: Observable<CalendarEvent[]>;

    constructor(private readonly apollo: Apollo) {}

    ngOnInit() {
        // use injected apollo service to run query
        // response JSON returns as { data: { events: [] } }
        // to get the calendarEvents$, map to the data.events
        this.calendarEvents$ = this.apollo.
            .watchQuery({ query: calendarEventsListQuery })
            .valueChanges.pipe(map(({ data }) => data.events));
    }
} 
```

超级简单。服务`Apollo`上的`valueChanges` getter 返回我们数据的一个可观察值。我们可以使用这个`map`操作符从返回的 JSON 数据中选择`events`。我们还可以通过将一个对象传递给`.watchQuery`方法中的`variables`属性来将变量传递给我们的查询。如果我们想传入一个像`first`这样的变量来获得匹配查询的第一批结果，更新查询以包含变量:

```
const calendarEventsListQuery = gql`
  query CalendarEventsQuery($first: Int!) {
    events(first: $first) {
      _id
      name
      description
      eventStart
      startTime
      eventEnd
      endTime
    }
  }
`; 
```

然后更新对`Apollo`服务的调用:

```
const variables = { first: 10 }
this.calendarEvents$ = this.apollo.
  .watchQuery({ query: calendarEventsListQuery, variables })
    .valueChanges.pipe(map(({ data }) => data.events)); 
```

点击查询阿波罗角度文档[了解更多信息。](https://www.apollographql.com/docs/angular/basics/queries/)

另一种，也是我更喜欢的，查询数据的方法是创建一个定制的服务提供者类，它扩展了`Query`并定义了我们的查询。`Query`是由`apollo-angular`库公开的一种类型，接受两种通用类型:响应类型和表示传递给查询的任何变量的类型。让我们从上面移走我们的日历事件列表查询，并为它构建一个查询服务:

```
import { Injectable } from '@angular/core';
import { Query } from 'apollo-angular';
import gql from 'graphql-tag';

import { CalendarEvent } from '../../models;

type CalendarEventsListResponse = {
    events: CalendarEvent[];
}

@Injectable()
export class CalendarEventsQuery extends Query<CalendarEventsListResponse> {
    document = gql`
        query CalendarEventsQuery {
            events {
                _id
                name
                description
                eventStart
                startTime
                eventEnd
                endTime
            }
        }
    `;
} 
```

因为这是一个服务提供者，并且被标注了`Injectable()`，所以我们需要将它提供给我们的模块，以使其可用于依赖注入。为此，将其添加到`NgModule`上的`providers`道具中

```
// imports
import { CalendarEventsQuery } from ‘./graphql’;

@NgModule({
    // declarations, imports, etc
    providers: [
        // other services
        CalendarEventsQuery
    ]
})
export class CalendarEventModule {} 
```

现在我们可以更新我们的容器组件:

```
//src/app/calendar-event/containers/calendar-events-list-container/calendar-events-list-container.component.ts
import { Component, OnInit } from '@angular/core';
import { Apollo } from 'apollo-angular';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';
import gql from 'graphql-tag';

import { CalendarEventsQuery } from '../../graphql;

@Component({
    selector: ‘app-calendar-events-list-container’,
    templateUrl: ‘./calendar-events-list-container.component.html’,
    styleUrls: [‘./calendar-events-list-container.component.scss’]
})
export class CalendarEventsListContainerComponent implements OnInit {
    calendarEvents$: Observable<CalendarEvent[]>;

    constructor(private readonly calendarEventsQuery: CalendarEventsQuery) {}

    ngOnInit() {
        // use injected apollo service to run query
        // response JSON returns as { data: { events: [] } }
        // to get the calendarEvents$, map to the data.events
        this.calendarEvents$ = this.calendarEventsQuery.watch().valueChanges.pipe(map({ data }) => data.events));
    }
} 
```

和第一个选项一样，我们也可以添加变量。首先，我们需要更新我们的`CalendarEventsQuery`服务类:

```
import { Injectable } from '@angular/core';
import { Query } from 'apollo-angular';
import gql from 'graphql-tag';

import { CalendarEvent } from '../../models;

type CalendarEventsListResponse = {
    events: CalendarEvent[];
}

export type CalendarEventsListVariables = {
    first: number;
}

@Injectable()
export class CalendarEventsQuery extends Query<CalendarEventsListResponse, CalendarEventsListVariables> {
    document = gql`
        query CalendarEventsQuery($first: Int!) {
            events(first: $first) {
                _id
                name
                description
                eventStart
                startTime
                eventEnd
                endTime
            }
        }
    `;
} 
```

现在，我们将变量传递给组件中注入的查询实例的`watch`方法:

```
//src/app/calendar-event/containers/calendar-events-list-container/calendar-events-list-container.component.ts
import { Component, OnInit } from '@angular/core';
import { Apollo } from 'apollo-angular';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';
import gql from 'graphql-tag';

import { CalendarEventsQuery } from '../../graphql;

@Component({
    selector: ‘app-calendar-events-list-container’,
    templateUrl: ‘./calendar-events-list-container.component.html’,
    styleUrls: [‘./calendar-events-list-container.component.scss’]
})
export class CalendarEventsListContainerComponent implements OnInit {
    calendarEvents$: Observable<CalendarEvent[]>;

    constructor(private readonly calendarEventsQuery: CalendarEventsQuery) {}

    ngOnInit() {
        // use injected apollo service to run query
        // response JSON returns as { data: { events: [] } }
        // to get the calendarEvents$, map to the data.events
        this.calendarEvents$ = this.calendarEventsQuery.watch({ first: 10  }).valueChanges.pipe(map({ data }) => data.events));
    }
} 
```

与第一个选项非常相似。这里的优点是，因为我们将查询分离到它自己的服务类中，所以我们可以将它注入到任何组件中来连接和运行我们的查询。它使得查询在多个组件想要使用数据的情况下可以重用。有关这种数据查询方法的更多信息，请查看这里的查询服务文档。

## 创建日历事件

查询都是关于获取数据的。约定是查询不应该改变数据平台中的任何数据。然而，创建、更新和删除数据几乎总是一项要求。GraphQL 用一个`Mutation`的概念来处理这个问题。变异在结构上与查询非常相似:您可以传入变异的名称、任何必要的变量和想要返回的数据。关键的区别在于，变异请求以关键字`mutation`开始，如果我们需要向变异传递输入(比如要创建/更新的对象),那么这个对象需要是一个`input`类型的对象。

### GraphQL 突变

让我们快速浏览一个来自[文档](https://graphql.org/learn/queries/#mutations)
的例子，首先让我们定义我们的输入类型，我们将把它作为变量传递给我们的突变:

```
input ReviewInput {
    stars: Int!
    commentary: String
} 
```

这里的关键是我们的`ReviewInput`类型是`input`。如果这个没有被声明为`input`，变异将会失败。这是对 GraphQL 最大的抱怨之一，因为它会导致重复的类型。我个人认为这很有帮助，因为它帮助我明确输入的形状以及创建或更新数据对象所需的内容。
现在来写我们的突变:

```
mutation CreateReviewForEpisode($ep: Episode!, $review: ReviewInput!) {
    createReview(episode: $ep, review: $review) {
        start
        commentary
    }
} 
```

这个应该看起来很眼熟。正如我提到的，请求的形式非常类似于查询。关键区别在于启动请求的关键字`mutation`。

### 在阿波罗的棱角中加入突变

现在是角度部分。就像上面的查询一样，我们可以使用`Apollo`服务，然后用`mutate({ mutation })`方法分派一个变异。但是，让我们使用一个`Mutation`服务提供商；代码可重用性等等。
我们将扩展的`Mutation`类非常类似于`Query`类，它接受两个泛型:变异的返回类型，以及表示传递给变异的变量的类型。

```
import { Injectable } from ‘@angular/core’; import { Mutation } from ‘apollo-angular’;
import gql from ‘graphql-tag’;

import { CalendarEvent } from ‘../../models’;

type CreateCalendarEventResponse = {
    createEvent: CalendarEvent;
};

type CreateCalendarEventVariables = {
    event: CalendarEvent;
};

@Injectable()
export class CreateCalendarEventMutation extends Mutation<CreateCalendarEventResponse, CreateCalendarEventVariables> {
    document = gql`
        mutation CreateCalendarEvent($event: CalendarEventInput!) {
            createEvent(event: $event) {
                _id
                status
                name
                description
                eventStart
                startTime
                eventEnd
                endTime
                location
                recurring
                guests {
                    _id
                    name
                    email
                    attending
                }
            }
        }
    `;
} 
```

超级直白。创建一个`mutation`请求对象，在那里我们可以传入通过`CreateCalendarEventVariables`类型提供的`$event`变量，给它命名为我们的变异- `createEvent` -引用变量，并列出我们想要返回的字段。

就像这个查询一样，因为它被标注为`@Injectable()`，我们需要把它提供给我们的模块，再次打开`calender-event.module.ts`，让我们把它添加到我们的`providers`道具:

```
// imports
import { CalendarEventsQuery, CreateCalendarEventMutation } from ‘./graphql’;

@NgModule({
    // declarations, imports, etc
    providers: [
        // other services
        CalendarEventsQuery,
        CreateCalendarEventMutation
    ]
})
export class CalendarEventModule {} 
```

创建一个容器组件来注入`CreateCalendarEventMutation`服务。它公开了一个`mutate`方法，我们将在其中传递我们的变量:`{ event: CalendarEvent }`，然后订阅结果。为了简单起见，在订阅时，我们将路由到`CalendarEventDetails`组件，该组件将显示新创建的`CalendarEvent` :
的详细信息

```
import { Component } from ‘@angular/core’; import { Router } from ‘@angular/router’; 
import { CalendarEvent } from ‘../../models’;
import { CreateCalendarEventMutation } from ‘../../graphql’;

@Component({
    selector: ‘app-create-calendar-event-container’,
    templateUrl: ‘./create-calendar-event-container.component.html’,
    styleUrls: [‘./create-calendar-event-container.component.scss’]
})
export class CreateCalendarEventContainerComponent {
    constructor(private readonly createCalendarEventMutation: CreateCalendarEventMutation, private readonly router: Router) {}

    createCalendarEvent(event: CalendarEvent) {
        this.createCalendarEventMutation.mutate({ event }).subscribe(({ created }) => {
            // created is the data.createdEvent response from the mutation
            // the type is CalendarEvent
            // route to the details page
            this.router.navigate([‘/calendar-events/details’, created._id]);
        }
    }
} 
```

当用事件数据调用`createCalendarEvent`函数时，比如用户从表单中输入数据，我们使用注入的`CreateCalendarEventMutation`实例来调度我们的带有`CalenderEvent`的变异。当它完成时，我们订阅应该包含所创建的`CalendarEvent`数据的结果，并导航到 event details 页面。

## 结论

阿波罗角图书馆还有更多。它在连接到 GraphQL API 的抽象、处理数据缓存以及提供方便的类来执行我们的查询和变化方面帮助很大。

本文仅涵盖了几个查询和变异的快速示例。查看[回购](https://github.com/cmwhited/event-calendar)了解更多信息，以及输入和保存`CalendarEvent`记录的表单。我希望这篇文章对你有所帮助，感谢阅读。

## 参考文献

*   [图 QL 学习文献](https://graphql.org/learn/)
*   [阿波罗角度文件](https://www.apollographql.com/docs/angular/)
*   [角状物质](https://material.angular.io/)

本文由 Chris Whited 撰写，他是 Dot 的高级软件工程师和导师。

你可以在推特上关注他们，地址是 [@cmwhited](https://dev.to/cmwhited) 。

需要 JavaScript 咨询、指导或培训帮助吗？在 [This Dot Labs](//thisdot.co) 查看我们的服务列表。