# io-multiplex

Once the socket communication is established, the most frequent operation is I/O between them. This brings two issues: one is not blocking on input from a specific source, and the other is how to minimize data copying between the kernel buffer and the data buffer.

The former requires I/O multiplexing, and the latter requires the use of some advanced I/O functions.


## io mux

### select

```basic
<sys/select.h>                FD_ZERO         select
                              FD_SET    +---> (int      , +-> fd + 1
 fd_set : 1024 bit            FD_CLR           fd_set*  , |-> read       fd_set
+-------------------------------------+        fd_set*  , |-> write      fd_set
|0 0 0 0 0 0 0 0 0 0 0 0 . . . . . . .|        fd_set*  , |-> exceptions fd_set
+-------------------------------------+        timeval* , +-> timeout  usually NULL
                              FD_ISSET  <---+ );
```

fd starts from 0, so nfds needs to be increased by 1.

after each call to `select`, it will clear the fds, and need to rejoin it!

thus, `select` is suitable for detecting only one socket fd. for more, we need `poll`.

by the way, fd_set is an example of bitmap.

### poll

```basic
<sys/poll.h>                  poll(struct pollfd *, unsigned long nfds, int timeout);

 struct pollfd {                        array            array index       INFTIM
     int   fd;
     short events;  <---+ register                    no limited to 1024
     short revents; +--->  detect
 };
```

### epoll

```basic
<sys/epoll.h>             + int epoll_create (int size);
struct epoll_event        |
{                         |                                  EPOLL_CTL_ADD 1
  uint32_t     events;    | event.events + = EPOLLET;    +>  EPOLL_CTL_DEL 2
  epoll_data_t data;      |                              |   EPOLL_CTL_MOD 3
};                        |                              +
                          | int epoll_ctl (int epfd, int op, int fd,struct epoll_event *event)
struct epoll_event event; |
event.data.fd = fd;       | int epoll_wait (int epfd, struct epoll_event *events,
event.events = EPOLLIN;   +                                   int maxevents, int timeout)

                        /------------+
in case       socket_fd-       threads       we need EPOLLONESHOT
                        \------------+

```



### compare





## refer

- *High Performance Linux Service Programming - chapter 9*
- *unpv1: the sockets networking api- chapter 6*
