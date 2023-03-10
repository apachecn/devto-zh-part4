# Nestjs，外部事件总线

> 原文：<https://dev.to/sergey_telpuk/nestjs-external-eventbus-5137>

朋友们好！在本文中，我想分享一个实现外部`EventBus`的解决方案。如果你还没有熟悉`EventBus`，可以访问[这个链接](https://docs.nestjs.com/recipes/cqrs)。默认情况下，Nestjs 只允许在本地调用`event handler`。当我们想要通过外部`EventBus`发布事件时，这是一个问题，所以我建议[下面的解决方案](https://github.com/sergey-telpuk/nestjs-external-eventbus)。贝娄，我给你看一个基于`redis-pub`的出版商。还有，我们可以为 RabbitMQ 找一个出版商。

`RedisPublisher`。

```
import {Injectable} from '@nestjs/common';
import {Client, ClientProxy, Transport} from '@nestjs/microservices';
import {AbstractPublisher} from '../abstract.publisher';

@Injectable()
export class RedisPublisher extends AbstractPublisher {
    TRANSPORT = Transport.REDIS;
    PATTERN = 'event_bus';

    @Client({
        transport: Transport.REDIS,
        options: {
            url: 'redis://:password123@redis:6379',
        },
    })
    client: ClientProxy;

    protected async send(pattern: any, data: any) {
        try {
            await this.client.send(pattern, data).toPromise();
        } catch (e) {
            this.log.error(e);
        }
    }
} 
```

`AbstractPublisher`

```
import {IEvent, IEventPublisher} from '@nestjs/cqrs';
import {Transport} from './transport.enum';
import {Injectable, Logger} from '@nestjs/common';

@Injectable()
export abstract class AbstractPublisher implements IEventPublisher {
    abstract TRANSPORT: Transport;
    abstract PATTERN: string;

    constructor(
        protected readonly log: Logger,
    ) {

    }

    publish<T extends IEvent>(event: T): void {

        const data = {
            payload: event,
            event: event.constructor.name,
        };

        this.send(this.PATTERN, data);
    }

    protected abstract send(pattern: any, data: any): any;
} 
```

如您所见，`RedisPublisher`在需要实现`send`方法的地方扩展了`AbstractPublisher`。对于特定类型的传输，我们应该实现自己的发送方法，并将传输添加到事件中。在一般情况下，通过传输开始发送事件就足够了。

`ContrivedEvent`

```
export class ContrivedEvent implements IEventWithTransport {
    TRANSPORTS = [Transport.RMQ, Transport.DEF, Transport.REDIS];

    constructor(
    ) {}
}
...
import {IEvent} from '@nestjs/cqrs';
import {Transport} from '../transport.enum';

export interface IEventWithTransport extends IEvent {
    TRANSPORTS: Transport[];
}
...

export enum Transport {
    TCP = 0,
    REDIS = 1,
    NATS = 2,
    MQTT = 3,
    GRPC = 4,
    RMQ = 5,
    DEF = 6,
} 
```

如您所见，使用了三种传输方式:

1.  兔脚
2.  雷迪斯
3.  当地的

使用`EventBusTransport` :

```
import {Controller, Get} from '@nestjs/common';
import {EventBusTransport} from '../event-bus-transport/event.bus.transport';
import {ContrivedEvent} from '../events/contrived/contrived.event';

@Controller('/')
export class ContrivedController {

    constructor(
        readonly eventBusTransport: EventBusTransport,
    ) {}

    @Get('/contrived')
     contrived() {
        this.eventBusTransport.publish(new ContrivedEvent());
    }

} 
```