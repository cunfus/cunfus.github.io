# socket-api

Socket is an abstraction layer between the applicantion layer and the transport layer. It abstracts complex operations of the TCP/IP layer into several simple interfaces for the application layer to call and implement processes to communicate in the network.

## socket api map

```basic
                                 +--------+
(AF_INET    SOCK_STREAM)         | socket |
 AF_INET6   SOCK_DGRAM           +--+-----+   setsockopt() ------ SO_REUSEADDR
                                    |                           | SO_RCVBUF     SO_SNDBUF
                 +------------------+------------------+        | SO_RCVLOWAT   SO_SNDLOWAT
                 |                                     | %      - SO_LINGER
                 |                                  +--+-----+
                 |                                  | bind   |  EADDRINUSE
                 |                                  +--+-----+
                 |                                     | %
                 |                                  +--+-----+
                 |                                  | listen |  ESTABLISHED QUEUE
               % |                                  +--+-----+  + SYN_RCVD  QUEUE < backlog
              +--+------+ syn ->                       |
ECONNREFUSED  | connect +------------------------------+
ETIMEDOUT     +--+------+                              | %#
                 |                                  +--+-----+
                 |            ( return one of       | accept |  (listening socket, ...)
                 |              connected sockets ) +--+-----+
               % |                                     | %#
              +--+-------------------------------------+-----+
              | write sendto                recvform  read   |  <sys/uio.h>
              |       sendmsg               recvmsg          |  readv
              | read                                  write  |  writev
              +--+-------------------------------------+-----+
               % |                                     | %#
               +-+---+                               +-+---+
               |close| fin ->                <- fin  |close|
               +-----+                               +-----+    # connfd
                                                         %      % sockfd


```

## macro define

Calling the API directly is too cumbersome, let's simplify it using macros.

```basic
/* struct sockaddr_in address */
#define init_address(address, ip, port)                \
    bzero(&address, sizeof(address));                  \
    address.sin_family = AF_INET;                      \
    inet_pton(AF_INET, ip, &address.sin_addr);         \
    address.sin_port = htons(port);

#define Socket() \
    socket(PF_INET, SOCK_STREAM, 0)

#define Bind(socket, server) \
    bind(socket, (struct sockaddr*)&server, (socklen_t)sizeof(server))

#define Listen(socket, n) \
    listen(socket, n)

#define Accept(socket, client, client_len) \
    accept(socket, (struct sockaddr*)&client, &client_len)

#define Connect(socket, server) \
    connect(socket, (struct sockaddr*)&server, (socklen_t)sizeof(server))

#define Close(socket) \
    close(socket);

#define Send(socket, buf) \
    send(socket, buf, strlen(buf), 0)

#define Recv(socket, buf) \
    recv(socket, buf, sizeof(buf) - 1, 0)


```

## boundary

Understand how the client and server start and terminate. What happens when certain errors occur, such as client host crash, client process crash, or network connection disruption? 

Only by understanding these boundary conditions and their interaction with the TCP/IP protocol can robust client and server programs be written to handle these situations.


### RST before accept

```basic

192.168.2.10.37120 > 192.168.2.10.54321: Flags [S], seq 3252709593, win 65495, 
        options [mss 65495,sackOK,TS val 759990870 ecr 0,nop,wscale 7], length 0
192.168.2.10.54321 > 192.168.2.10.37120: Flags [S.], seq 139043462, ack 3252709594, win 65483,
        options [mss 65495,sackOK,TS val 759990870 ecr 759990870,nop,wscale 7], length 0
192.168.2.10.37120 > 192.168.2.10.54321: Flags [.], ack 1, win 512, 
        options [nop,nop,TS val 759990870 ecr 759990870], length 0
192.168.2.10.37120 > 192.168.2.10.54321: Flags [R.], seq 1, ack 1, win 512, 
        options [nop,nop,TS val 759990870 ecr 759990870], length 0


```

### server terminated

```basic
192.168.2.10.58730 > 192.168.2.10.54321: Flags [S], seq 559678739, win 65495, 
        options [mss 65495,sackOK,TS val 800516176 ecr 0,nop,wscale 7], length 0
192.168.2.10.54321 > 192.168.2.10.58730: Flags [S.], seq 2091989194, ack 559678740, win 65483,
        options [mss 65495,sackOK,TS val 800516176 ecr 800516176,nop,wscale 7], length 0
192.168.2.10.58730 > 192.168.2.10.54321: Flags [.], ack 1, win 512, 
        options [nop,nop,TS val 800516176 ecr 800516176], length 0

192.168.2.10.58730 > 192.168.2.10.54321: Flags [P.], seq 1:7, ack 1, win 512, 
        options [nop,nop,TS val 800532435 ecr 800516176], length 6
192.168.2.10.54321 > 192.168.2.10.58730: Flags [.], ack 7, win 512, 
        options [nop,nop,TS val 800532435 ecr 800532435], length 0
192.168.2.10.54321 > 192.168.2.10.58730: Flags [P.], seq 1:7, ack 7, win 512, 
        options [nop,nop,TS val 800532435 ecr 800532435], length 6
192.168.2.10.58730 > 192.168.2.10.54321: Flags [.], ack 7, win 512, 
        options [nop,nop,TS val 800532435 ecr 800532435], length 0

192.168.2.10.54321 > 192.168.2.10.58730: Flags [F.], seq 7, ack 7, win 512, 
        options [nop,nop,TS val 800645539 ecr 800532435], length 0
192.168.2.10.58730 > 192.168.2.10.54321: Flags [.], ack 8, win 512, 
        options [nop,nop,TS val 800645586 ecr 800645539], length 0

192.168.2.10.58730 > 192.168.2.10.54321: Flags [P.], seq 7:12, ack 8, win 512, 
        options [nop,nop,TS val 800775642 ecr 800645539], length 5
192.168.2.10.54321 > 192.168.2.10.58730: Flags [R], seq 2091989202, win 0, length 0


```

### server host crash

It will retransmit before timeout.

```basic
192.168.2.2.53050 > 192.168.2.10.54321: Flags [P.], seq 17:19, ack 17, win 502, 
        options [nop,nop,TS val 2312873962 ecr 1837083938], length 2
192.168.2.2.53050 > 192.168.2.10.54321: Flags [P.], seq 17:19, ack 17, win 502, 
        options [nop,nop,TS val 2312874549 ecr 1837083938], length 2
192.168.2.2.53050 > 192.168.2.10.54321: Flags [P.], seq 17:19, ack 17, win 502, 
        options [nop,nop,TS val 2312875541 ecr 1837083938], length 2
192.168.2.2.53050 > 192.168.2.10.54321: Flags [P.], seq 17:19, ack 17, win 502, 
        options [nop,nop,TS val 2312877525 ecr 1837083938], length 2
192.168.2.2.53050 > 192.168.2.10.54321: Flags [P.], seq 17:19, ack 17, win 502, 
        options [nop,nop,TS val 2312881557 ecr 1837083938], length 2

```

## thoughts

Transitioning naturally from TCP/IP protocol to socket API, we see that the existing I/O operations are both cumbersome and inefficient. 

So the problem in socket usage is how to handle I/O more effectively.


## refer

- *High Performance Linux Service Programming - chapter 5*
- *unpv1: the sockets networking api- chapter 5*

