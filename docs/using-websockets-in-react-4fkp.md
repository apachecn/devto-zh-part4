# 在 React 中使用 WebSockets

> 原文：<https://dev.to/finallynero/using-websockets-in-react-4fkp>

最近，我不得不在我正在开发的 React 应用程序中使用 WebSocket API，因此在本文中，我将简要介绍如何在 React 应用程序中使用它。

## 什么是 Websocket

根据 MDN 的说法，*web socket API 是一种先进的技术，可以在用户的浏览器和服务器之间开启双向互动通信会话。使用这个 API，您可以向服务器发送消息并接收事件驱动的响应，而不必轮询服务器的回复*，简单地说，WebSocket 可以帮助您保持客户端(在我的例子中是 React 应用程序)和服务器之间的双向通信。

## 我为什么需要 WebSocket

我参与的一个项目要求我每 30 秒向服务器发送一次 ping，告诉服务器应用程序仍然在线，同时跟踪登录到应用程序的用户以及他们在线的时间。我不想谈论太多细节，但是我需要不断地与服务器“交流”,为此使用 REST API 是低效的。

## React 的基本设置

通常，我尝试只使用组件树中的一个 WebSocket 实例，然后将它传递给其他需要使用 WebSocket 实例来监听或发送消息到服务器的组件；这是假设您在子组件中监听特定的 WebSocket 实例。

```
class Main extends Component {
    ......

    // instance of websocket connection as a class property
    ws = new WebSocket('ws://localhost:3000/ws')

    componentDidMount() {
        this.ws.onopen = () => {
        // on connecting, do nothing but log it to the console
        console.log('connected')
        }

        this.ws.onmessage = evt => {
        // listen to data sent from the websocket server
        const message = JSON.parse(evt.data)
        this.setState({dataFromServer: message})
        console.log(message)
        }

        this.ws.onclose = () => {
        console.log('disconnected')
        // automatically try to reconnect on connection loss

        }

    }

    render(){
        <ChildComponent websocket={this.ws} />
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码片段中，我将其称为`Main`组件，因为我认为它应该像需要使用 WebSocket 实例的子组件的父组件一样。首先，我们创建一个 WebSocket 的新实例作为类属性`ws`。然后在`componentDidMount`方法中，我们可以订阅并监听 WebSocket 提供给我们的一些事件。

*   `onopen`:当 WebSocket 连接建立后，调用 onopen 事件监听器。
*   `onmessage`:从服务器接收数据时，发送 onmessage 事件。
*   `onclose`:关闭 WebSocket 连接时调用 onclose 监听器。

所以所有这些都在`componentDidMount`中设置，因为我们希望这些事件监听器在组件呈现在 DOM 中时可用。此外，我们可以像在`<ChildComponent/>`中一样将 WebSocket 的实例作为道具传递给子组件，这样我们就可以监听 WebSocket 实例上的任何事件，而不必在我们需要使用 WebSocket 的每个组件中创建新的实例。

但这种设置有一个问题，一旦出现错误或 WebSocket 连接由于某种原因关闭，即服务器关闭或网络问题等，连接将不会重新建立，直到您再次调用`componentDidMount`可能是通过刷新页面。我不认为这是我们想要的。

## 高级设置

这个设置改编自两个 StackOverflow 答案，[如何在关闭连接后重新连接 web socket](https://stackoverflow.com/questions/3780511/reconnection-of-client-when-server-reboots-in-websocket/37038217#37038217)和 [WebSocket:如何在它死亡后自动重新连接](https://stackoverflow.com/questions/22431751/websocket-how-to-automatically-reconnect-after-it-dies)。

```
class Main extends Component {
    constructor(props) {
        super(props);

        this.state = {
            ws: null
        };
    }

    // single websocket instance for the own application and constantly trying to reconnect.

    componentDidMount() {
        this.connect();
    }

    timeout = 250; // Initial timeout duration as a class variable

    /**
     * @function connect
     * This function establishes the connect with the websocket and also ensures constant reconnection if connection closes
     */
    connect = () => {
        var ws = new WebSocket("ws://localhost:3000/ws");
        let that = this; // cache the this
        var connectInterval;

        // websocket onopen event listener
        ws.onopen = () => {
            console.log("connected websocket main component");

            this.setState({ ws: ws });

            that.timeout = 250; // reset timer to 250 on open of websocket connection 
            clearTimeout(connectInterval); // clear Interval on on open of websocket connection
        };

        // websocket onclose event listener
        ws.onclose = e => {
            console.log(
                `Socket is closed. Reconnect will be attempted in ${Math.min(
                    10000 / 1000,
                    (that.timeout + that.timeout) / 1000
                )} second.`,
                e.reason
            );

            that.timeout = that.timeout + that.timeout; //increment retry interval
            connectInterval = setTimeout(this.check, Math.min(10000, that.timeout)); //call check function after timeout
        };

        // websocket onerror event listener
        ws.onerror = err => {
            console.error(
                "Socket encountered error: ",
                err.message,
                "Closing socket"
            );

            ws.close();
        };
    };

    /**
     * utilited by the @function connect to check if the connection is close, if so attempts to reconnect
     */
    check = () => {
        const { ws } = this.state;
        if (!ws || ws.readyState == WebSocket.CLOSED) this.connect(); //check if websocket instance is closed, if so call `connect` function.
    };

    render() {
        return <ChildComponent websocket={this.state.ws} />;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的高级设置只是确保 WebSocket 在服务器宕机或网络故障时总是尝试连接，因此每当服务器恢复时，客户端都会重新连接。

我将运行这个设置，调用`connect`方法来启动`componentDidMount`中的 WebSocket 连接。声明了一个名为`timeout`的类属性，并将其设置为 250 毫秒，然后我们有两个函数`connect`和`check`，我将详细介绍这些函数的作用。

*   `check` -该函数用于检查是否没有 WebSocket 实例或者 WebSocket 连接是否关闭，如果是，则调用`connect`函数。

*   `connect` -这个函数基本上是管理 WebSocket 连接，这里我们监听`onopen`、`onclose`和`onerror`事件。在`onopen`监听器中，websocket 实例被添加到状态中，因此它可以作为道具传递给想要监听它的子组件。那么超时变量被设置回`250ms`并且设置间隔被清除。

在`onclose`监听器中，超时值增加，并且在 setTimeout 中调用具有增加的超时值的`check`函数，一旦超时值大于 10000ms(10 秒),它就停止增加。我这样做是为了防止主动尝试重新连接到服务器，相反，它在尝试重新连接之前会延迟一段时间。

有一些库可以帮助你实现这一点，比如[重新连接 WebSocket](https://github.com/joewalnes/reconnecting-websocket) ，但是这个库和我的设置没有实现指数补偿算法，该算法可以在大量客户端试图重新连接到服务器时帮助管理服务器泛滥。一个名为 [@gamestdio/websocket](https://github.com/gamestdio/websocket) 的库实现了指数回退，所以如果你正在处理大量的客户端应用程序，你可以使用它。

## 用 WebSocket 发送消息

在这个高级设置中有一个问题，当 WebSocket 连接关闭时`onclose`监听器 WebSocket 实例可能会被设置为`null`一段时间，这样做的原因是为了确保我们不会在连接关闭后再次打开时打开新的 WebSocket 实例。这里的问题是，如果你试图在 websocket 实例为空时向服务器发送数据，这可能会破坏你的应用程序，那么我们如何解决这个问题呢？答案是在组件中任何需要发送数据的地方使用 try catch 块。

```
 class ChildComponent extends Component {

    sendMessage=()=>{
        const {websocket} = this.props // websocket instance passed as props to the child component.

        try {
            websocket.send(data) //send data to the server
        } catch (error) {
            console.log(error) // catch error
        }
    }
    render() {
        return (
            <div>
                ........
            </div>
        );
    }
}

export default ChildComponent; 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

我希望这篇教程能帮助你在 react 应用程序中设置 WebSockets，因为这是我写这篇文章的动机。我是在客户端使用 WebSockets 的新手，所以如果你认为有什么可以做得更好，请发表评论。