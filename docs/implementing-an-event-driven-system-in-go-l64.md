# 在 Go 中实现事件驱动系统

> 原文：<https://dev.to/stephenafamo/implementing-an-event-driven-system-in-go-l64>

[![Events in Go](img/0547208df98fc94c26734da08e3b4daf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E7E09nKR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/stephenafamo.com/blog/wp-content/uploads/2019/09/Events-in-Go.jpg%3Ffit%3D1500%252C750%26ssl%3D1)

在这篇文章中，我们将在围棋中实现一个事件驱动的系统。

我们将设想一个虚构的应用程序，我们希望在创建新帐户时发送事件，在删除帐户时发送事件。

让我们假设我们程序的当前结构是这样的:

```
working-dir
|
|__auth.go/
| |__auth.go
|
|__main.go
|__go.mod
|__go.sum 
```

Enter fullscreen mode Exit fullscreen mode

我们希望该系统能够:

*   保持类型安全。没有`interface{}`，不需要类型转换。
*   能够为每个事件定义一个自定义的有效负载。

## 定义事件

为了安全起见，我们将在一个单独的包中创建事件，并且只导出完整的事件。让我们把这个包称为事件。我们将这样更新我们的目录结构。

```
working-dir
|
|__auth.go/
| |__auth.go
|
|__events/
|__main.go
|__go.mod
|__go.sum 
```

Enter fullscreen mode Exit fullscreen mode

每个事件将是唯一的类型，并且将定义每个事件所需的有效载荷。每个处理程序都明确知道它将接收什么数据。

下面是我们创建用户时的事件:

```
// events/user_created.go
package events

import (
    "time"
)

var UserCreated userCreated

// UserCreatedPayload is the data for when a user is created
type UserCreatedPayload struct {
    Email string
    Time  time.Time
}

type userCreated struct {
    handlers []interface{ Handle(UserCreatedPayload) }
}

// Register adds an event handler for this event
func (u *userCreated) Register(handler interface{ Handle(UserCreatedPayload) }) {
    u.handlers = append(u.handlers, handler)
}

// Trigger sends out an event with the payload
func (u userCreated) Trigger(payload UserCreatedPayload) {
    for _, handler := range u.handlers {
        go handler.Handle(payload)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以为删除用户创建另一个事件:

```
// events/user_deleted.go
package events

import (
    "time"
)

var UserDeleted userDeleted

// UserDeletedPayload is the data for when a user is Deleted
type UserDeletedPayload struct {
    Email string
    Time  time.Time
}

type userDeleted struct {
    handlers []interface{ Handle(UserDeletedPayload) }
}

// Register adds an event handler for this event
func (u *userDeleted) Register(handler interface{ Handle(UserDeletedPayload) }) {
    u.handlers = append(u.handlers, handler)
}

// Trigger sends out an event with the payload
func (u userDeleted) Trigger(payload UserDeletedPayload) {
    for _, handler := range u.handlers {
        go handler.Handle(payload)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的目录结构现在看起来像这样:

```
working-dir
|
|__auth.go/
|   |__auth.go
|
|__events/
|   |__user_created.go
|   |__user_deleted.go
|
|__main.go
|__go.mod
|__go.sum 
```

Enter fullscreen mode Exit fullscreen mode

这个系统的一个优点是事件变量类型没有被导出，因此，它们不能被改变或者分配给包外的不同的东西。

## 监听事件

为了监听事件，我们导入我们的`events`包，然后导入`register`我们的事件处理程序。

首先，我们创建一个监听器，它在创建用户时向管理员和 slack 发送通知。

```
// create_notifier.go
package main

import (
    "time"

    "github.com/stephenafamo/demo/events"
)

func init() {
    createNotifier := userCreatedNotifier{
        adminEmail: "the.boss@example.com",
        slackHook: "https://hooks.slack.com/services/...",
    }

    events.UserCreated.Register(createNotifier)
}

type userCreatedNotifier struct{
    adminEmail string
    slackHook string
}

func (u userCreatedNotifier) notifyAdmin(email string, time time.Time) {
    // send a message to the admin that a user was created
}

func (u userCreatedNotifier) sendToSlack(email string, time time.Time) {
    // send to a slack webhook that a user was created
}

func (u userCreatedNotifier) Handle(payload events.UserCreatedPayload) {
    // Do something with our payload
    u.notifyAdmin(payload.Email, payload.Time)
    u.sendToSlack(payload.Email, payload.Time)
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们添加另一个侦听器，当用户被删除时，它会做同样的事情。

```
// delete_notifier.go
package main

import (
    "time"

    "github.com/stephenafamo/demo/events"
)

func init() {
    createNotifier := userCreatedNotifier{
        adminEmail: "the.boss@example.com",
        slackHook: "https://hooks.slack.com/services/...",
    }

    events.UserCreated.Register(createNotifier)
}

type userCreatedNotifier struct{
    adminEmail string
    slackHook string
}

func (u userCreatedNotifier) notifyAdmin(email string, time time.Time) {
    // send a message to the admin that a user was created
}

func (u userCreatedNotifier) sendToSlack(email string, time time.Time) {
    // send to a slack webhook that a user was created
}

func (u userCreatedNotifier) Handle(payload events.UserCreatedPayload) {
    // Do something with our payload
    u.notifyAdmin(payload.Email, payload.Time)
    u.sendToSlack(payload.Email, payload.Time)
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们将有一个类似于这样的目录结构:

```
working-dir
|
|__auth.go/
|   |__auth.go
|
|__events/
|   |__user_created.go
|   |__user_deleted.go
|
|__create_notifier.go
|__delete_notifier.go
|__main.go
|__go.mod
|__go.sum 
```

Enter fullscreen mode Exit fullscreen mode

## 触发事件

既然我们已经设置了监听器，那么我们就可以从我们的`auth`包(或任何其他地方)触发这些事件。

```
// auth.go
package auth

import (
    "time"

    "github.com/stephenafamo/demo/events"
    // Other imported packages
)

func CreateUser() {
    // ...
    events.UserCreated.Trigger(events.UserCreatedPayload{
        Email: "new.user@example.com",
        Time: time.Now(),
    })
    // ...
}

func DeleteUser() {
    // ...
    events.UserDeleted.Trigger(events.UserDeletedPayload{
        Email: "deleted.user@example.com",
        Time: time.Now(),
    })
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我们看到了一种以类型安全的方式定义事件的方法，如何监听这些事件以及如何触发它们。

没什么特别的。正如所有的事情一样，好的解决方案是一个无聊的解决方案。

在 Go 中实现事件驱动系统的帖子[首先出现在](https://stephenafamo.com/blog/implementing-an-event-driven-system-in-go/)[斯蒂芬·阿法莫的博客](https://stephenafamo.com/blog)上。