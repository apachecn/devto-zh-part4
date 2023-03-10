# 定制挂钩和插座

> 原文：<https://dev.to/droidmakk/custom-hooks-and-sockets-2i56>

所以，自从钩子出现已经有一段时间了，我一直在努力找时间尝试和学习创建定制钩子。真正令人难以置信的🧠.虽然有趣的部分是，这个概念可能很简单，但如果你找到正确的用例。

我想尝试的第一件事是发出网络请求，看看我们如何使用钩子来实现。通常的反应方式显然是 **setState** 。假设你们大多数人都使用过 setState。现在让我们看看如何使用**钩子**在功能组件中编写它。

为此，让我们使用 JSON 占位符来获取帖子。

```
import React,{ useState } from "react";

// URL to fetch posts
const postsUrl = "https://jsonplaceholder.typicode.com/posts"

// Our Component
const ListPosts = () => {
    // Using state hook to set if loading
    const [ loading, setLoading ] = useState(true);
    // Fetch and set list of posts
    const [ posts, setPosts ] = useState([]);

    //Here we fetch the posts and add it to posts state
    fetch(postsUrl)
        .then(res => res.json())
        .then(data => { setPosts(data); setLoading(false); })
        .catch(err => {
            alert('Error fetching posts');
            setLoading(false);
        });

    //Our Component which will lists posts
    return(
        <div>
            // Loop through the list of posts
            <h1>My Posts <span>{posts.length}</span></h1>
            {loading ? 
                <h1>Loading posts...</h2> 
                : posts.map(post => (
                    <div>
                        <h3>{post.title}</h3>
                        <hr/>
                        <p>{post.body}</p>
                    </div>
            })}
        </div>
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个组件中，我们使用了 **useState** 钩子来设置帖子和加载状态。但是现在让我们看看如何将它重写为一个将逻辑与组件分离的钩子。

首先让我们使用 effect 钩子，它将对组件内部的变化做出反应。我们去看看。

```
React.useEffect(() => {
    // Function called whenever something updates in component
},[ /* Update based on variable */ ]) 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们看看如何在我们的组件
中使用它

```
import React,{ useState } from "react";

// URL to fetch posts
const postsUrl = "https://jsonplaceholder.typicode.com/posts"

// Our Component
const ListPosts = () => {
    // Using state hook to set if loading
    const [ loading, setLoading ] = useState(true);
    // Fetch and set list of posts
    const [ posts, setPosts ] = useState([]);

    // Use effect to update component
    React.useEffect(() => {
        //Here we fetch the posts and add it to posts state
        fetch(postsUrl)
            .then(res => res.json())
            .then(data => { setPosts(data); setLoading(false); })
            .catch(err => {
                alert('Error fetching posts');
                setLoading(false);
            });
    },[ postsUrl ])

    //Our Component which will lists posts
    return(
        <div>
            // Loop through the list of posts
            <h1>My Posts <span>{posts.length}</span></h1>
            {loading ? 
                <h1>Loading posts...</h2> 
                : posts.map(post => (
                    <div>
                        <h3>{post.title}</h3>
                        <hr/>
                        <p>{post.body}</p>
                    </div>
            })}
        </div>
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们将逻辑从应用程序中分离出来

```
// Here is our hook which makes request and returns the posts
const usePosts = (postUrl) => {
    const [ loading, setLoading ] = useState(true);
    const [ posts, setPosts ] = useState([]);

    React.useEffect(() => {
        //Here we fetch the posts and add it to posts state
        fetch(postsUrl)
            .then(res => res.json())
            .then(data => { setPosts(data); setLoading(false); })
            .catch(err => {
                alert('Error fetching posts');
                setLoading(false);
            });
    },[ postsUrl ])

    return { loading, posts }
}

// Adding it to our component
const postsUrl = "https://jsonplaceholder.typicode.com/posts";

const ListPosts = () => {
    const { loading, posts } = usePosts(postsUrl);

    return(  
        <div>  // Loop through the list of posts  
            <h1>My Posts <span>{posts.length}</span></h1>  
            {loading ?  
                <h1>Loading posts...</h2>
                : posts.map(post =>  (
                <div>
                    <h3>{post.title}</h3>  
                    <hr/>
                    <p>{post.body}</p>
                </div>
                })}  
        </div>)
} 
```

Enter fullscreen mode Exit fullscreen mode

好了，我们已经创建了第一个简单的定制钩子。如果我们打算使用静态请求来获取数据，这是可以的。如果我们要接收基于事件的数据流呢？
让我们看看如何使用套接字编写相同的组件来从物联网设备获取温度。它会不断地发送数据。

为此，我创建了一个示例代码，它使用 express 和 socket-io 传输 0 到 100 之间的随机温度。代码如下。

```
// server.js
const app = require('express')();
const server = require('http').createServer(app);
const socket = require('socket.io');
const io = socket(server);

const port = 8080 || process.env.PORT;

io.on('connection', () => {
    console.info('SOME ONE IS HERE');
});

setInterval(() => {
    const temp = Math.floor(Math.random()* 100);
    const topic = 'temperature';
    console.info(`TEMP : ${temp}`);
    io.emit(topic,temp);
}, 3000);

const listenCb = () => console.table([['status', 'port'],['started',port]])
server.listen(port, listenCb); 
```

Enter fullscreen mode Exit fullscreen mode

使用下面的代码
安装依赖项并运行服务器

```
npm i -S socket.io express

# Run the app using nodejs
node app.js 
```

Enter fullscreen mode Exit fullscreen mode

这将持续每 3 秒发送一次数据。

现在让我们看看如何在 React 的组件中使用定制钩子来实现这一点。首先让我们写我们的钩子。

```
import React from 'react';
import PropTypes from 'prop-types';
import socket from 'socket.io-client';

// Use socket to fetch request to data 
// Socket server's url and topic in which data is sent
const useSocket = (serverUrl, topic) => {
    const [temp, setTemp] = React.useState(0);
    const [isConnected, setConnected] = React.useState(false);

    React.useEffect(() => {
        const client = socket.connect(serverUrl);
        client.on("connect", () => setConnected(true));
        client.on("disconnect", () => setConnected(false));
        client.on(topic, (data) => {
            setTemp(data);
        })
    }, [serverUrl, topic, isConnected]);

    return { temp, isConnected };
}

// Our component which sends the request through the topic
const Sockt = () => {
    const serverUrl='http://localhost:8080', topic='temperature';
    const { temp, isConnected } = useSocket(serverUrl, topic);

    return (
        <div>
            <h4>Temperature</h4>
            <h1>{temp}</h1>
            <h3>{`CONNECTED: ${isConnected}`}</h3>
        </div>
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

组件现在将显示从套接字服务器接收的温度。每次通过套接字发送数据时。

我希望，你能学到一些新的有趣的东西。找到代码沙箱来实现我们在整篇文章中所讨论的内容。

https://codesandbox.io/s/express-socket-io-70t5x 后端套接字服务器🧦
t1

用钩子做出反应
[https://codesandbox.io/s/priceless-greider-3b814](https://codesandbox.io/s/priceless-greider-3b814)