# 一个带 kqueue 的 TCP 服务器

> 原文：<https://dev.to/frevib/a-tcp-server-with-kqueue-527>

## 内核队列(kqueue)

如果您使用异步(非阻塞)框架编程，那么这个框架很可能在幕后使用内核轮询 API。内核轮询 API 使用无限循环来检查事件，并在事件发生时通知调用者。例如，当一个 HTTP 请求被触发时，在循环的每次迭代中，都要检查数据是否已经从服务器返回。主要的操作系统以不同的方式实现了内核轮询:

*   Linux 使用`epoll`
*   BSD 和 OSX 使用`kqueue`
*   Windows 使用`IOCP`

`epoll`和`kqueue`在功能上几乎相同，但是有一些 API 差异。让我们写一个简单的 TCP 服务器来理解`kqueue`是如何工作的。

## 创建 kqueue TCP 服务器的步骤

*   创建一个监听套接字。这包括用`socket(2)`创建套接字，用`bind(2)`将套接字绑定到 IP 和 port。

*   用帮助宏`EV_SET`创建我们想要监控的`kevent`。然后用`kqueue(2)`创建 kqueue 本身，并使用`kevent(2)`将`kevent`添加到 kqueue 中。

*   创建一个“事件循环”。这只是一个无限循环，它检查新客户机是否已经连接到我们的监听套接字，并使用`accept(8)`为客户机的实际连接创建一个新套接字。

代码包括 Makefile 在 [github](https://github.com/frevib/kqueue-tcp-server)

```
#include <stdio.h>
#include <stdlib.h>
#include <netinet/in.h>
#include <sys/socket.h>
#include <sys/event.h>
#include <string.h>
#include <unistd.h> 
int main()
{
    // All needed variables.
    int socket_listen_fd,
        portno = 1815,
        client_len,
        socket_connection_fd,
        kq,
        new_events;
    struct kevent change_event[4],
        event[4];
    struct sockaddr_in serv_addr,
        client_addr;

    // Create socket.
    if (((socket_listen_fd = socket(AF_INET, SOCK_STREAM, 0)) < 0))
    {
        perror("ERROR opening socket");
        exit(1);
    }

    // Create socket structure and bind to ip address.
    bzero((char *)&serv_addr, sizeof(serv_addr));
    serv_addr.sin_family = AF_INET;
    serv_addr.sin_addr.s_addr = INADDR_ANY;
    serv_addr.sin_port = htons(portno);

    if (bind(socket_listen_fd, (struct sockaddr *)&serv_addr, sizeof(serv_addr)) < 0)
    {
        perror("Error binding socket");
        exit(1);
    }

    // Start listening.
    listen(socket_listen_fd, 3);
    client_len = sizeof(client_addr);

    // Prepare the kqueue.
    kq = kqueue();

    // Create event 'filter', these are the events we want to monitor.
    // Here we want to monitor: socket_listen_fd, for the events: EVFILT_READ 
    // (when there is data to be read on the socket), and perform the following
    // actions on this kevent: EV_ADD and EV_ENABLE (add the event to the kqueue 
    // and enable it).
    EV_SET(change_event, socket_listen_fd, EVFILT_READ, EV_ADD | EV_ENABLE, 0, 0, 0);

    // Register kevent with the kqueue.
    if (kevent(kq, change_event, 1, NULL, 0, NULL) == -1)
    {
        perror("kevent");
        exit(1);
    }

    // Actual event loop.
    for (;;)
    {
        // Check for new events, but do not register new events with
        // the kqueue. Hence the 2nd and 3rd arguments are NULL, 0.
        // Only handle 1 new event per iteration in the loop; 5th
        // argument is 1.
        new_events = kevent(kq, NULL, 0, event, 1, NULL);
        if (new_events == -1)
        {
            perror("kevent");
            exit(1);
        }

        for (int i = 0; new_events > i; i++)
        {
            int event_fd = event[i].ident;

            // When the client disconnects an EOF is sent. By closing the file
            // descriptor the event is automatically removed from the kqueue.
            if (event[i].flags & EV_EOF)
            {
                printf("Client has disconnected");
                close(event_fd);
            }
            // If the new event's file descriptor is the same as the listening
            // socket's file descriptor, we are sure that a new client wants 
            // to connect to our socket.
            else if (event_fd == socket_listen_fd)
            {
                // Incoming socket connection on the listening socket.
                // Create a new socket for the actual connection to client.
                socket_connection_fd = accept(event_fd, (struct sockaddr *)&client_addr, (socklen_t *)&client_len);
                if (socket_connection_fd == -1)
                {
                    perror("Accept socket error");
                }

                // Put this new socket connection also as a 'filter' event
                // to watch in kqueue, so we can now watch for events on this
                // new socket.
                EV_SET(change_event, socket_connection_fd, EVFILT_READ, EV_ADD, 0, 0, NULL);
                if (kevent(kq, change_event, 1, NULL, 0, NULL) < 0)
                {
                    perror("kevent error");
                }
            }

            else if (event[i].filter & EVFILT_READ)
            {
                // Read bytes from socket
                char buf[1024];
                size_t bytes_read = recv(event_fd, buf, sizeof(buf), 0);
                printf("read %zu bytes\n", bytes_read);
            }
        }
    }

    return 0;
} 
```