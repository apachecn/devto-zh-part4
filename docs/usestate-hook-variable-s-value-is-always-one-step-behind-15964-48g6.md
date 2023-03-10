# useState 挂钩变量的值总是落后一步

> 原文：<https://dev.to/marcosdipaolo/usestate-hook-variable-s-value-is-always-one-step-behind-15964-48g6>

这里使用 React 和 Next.js。

下面是代码的摘要:

我有这个钩子:

```
import { useState } from 'react';
import BigCalendar from 'react-big-calendar'
import moment from 'moment';
import CreateEventForm from './CreateEventForm';

const localizer = BigCalendar.momentLocalizer(moment);

const Calendar = props => {

    const [newEvent, setNewEvent] = useState({
        start: new Date(),
        end: new Date(),
    });

    const onSelectHandler = info => {
        // logging the new info
        console.warn('New info: ', info);
        // assigning the new info to the hooks variable
        setNewEvent(info);
        // logging the hook variable's value 
        console.warn('Hook variable\'s value: ', newEvent);
    }

} 
```

实际上没什么要告诉你的，当事件触发处理程序时，变量 info 会带有一个关于新信息的对象。我立即将信息记录到控制台，以查看它实际上是正确的新信息，我将信息设置为钩子的变量，并立即将钩子变量的值记录到控制台，它与我刚刚试图分配给它的新信息不匹配。它实际上记录了默认值(2 个日期)。如果我再做一次，会发生同样的情况，但是这次钩子变量没有记录默认值，而是记录第一次尝试的新信息，所以基本上每次都会晚一次。

我能做些什么让这一切发生呢？

当我在 React 大日历
上选择一个时间段时，触发处理程序的事件发生

```
const myEvents = props.events.map(event => {
    return {
        ...event,
        start: new Date(event.start),
        end: new Date(event.end)
    };
});

    <BigCalendar
        defaultView="week"
        localizer={localizer}
        events={myEvents}
        startAccessor="start"
        endAccessor="end"
        selectable={true}
        onSelectSlot={onSelectHandler}
    /> 
```