# tun-tap

## 原理

```basic
+---------------+         +---------------+         +---------------+
| application A |         | application B |         | application C |
+-------^-------+         +-------+-------+         +-------+-------+       user-mode
        |                         |                         |
+-----------------------------------------------------------------------------------+
        |                         |                         |
        |                 +-------v-------------------------v-------+     kernel-mode
        |                 |               layer socket              |
        |                 +--------------------^--------------------+
        |                                      |
        |                 +--------------------v--------------------+
        |                 |              TCP/IP stack               |
        |                 +--------^-----------------------^--------+
        |                          |                       |
+-------v-------+         +--------v--------+     +--------v--------+
| /dev/net/tun  |         | Drivers for     |     | Drivers for     |
|   char drivers|         |    virtual NIC  |     |   pysical NIC   |
+-------^-------+         +--------^--------+     +--------^--------+
        |                          |                       |
        |                 +--------v--------+     +--------v--------+
        |        (tun tap)|    virtual NIC  |     |   pysical NIC   |
        |                 +--------^--------+     +--------^--------+
        |                          |                       |
        |                          |                       |
        +--------------------------+                       |
                                                           |
+-----------------------------------------------------------------------------------+
                                                           |
                                      +--------------+     |
                                      |   Internet   <-----+
                                      +--------------+

```



## 实践

### code

```c
#include <linux/if.h>
#include <linux/if_tun.h>
#include <sys/ioctl.h>

#include <fcntl.h>
#include <unistd.h>
#include <string.h>
#include <stdlib.h>
#include <stdio.h>

int tun_alloc(char *dev, int flags)
{
    struct ifreq ifr;
    int fd, err;
    char *clonedev = "/dev/net/tun";

    /* 打开 /dev/net/tun */
    if ((fd = open(clonedev, O_RDWR)) < 0) {
        return fd;
    }

    memset(&ifr, 0, sizeof(ifr));
    ifr.ifr_flags = flags;

    /* 未指定设备名称时，由内核分配设备名 */
    if (*dev) {
        strncpy(ifr.ifr_name, dev, IFNAMSIZ);
    }

    /* 通过 ioctl() 向内核注册网络设备，完成创建虚拟接口
     * 通过文件描述符 fd 关联，与接口通信 */
    if ( (err = ioctl(fd, TUNSETIFF, (void *)&ifr)) < 0) {
        close(fd);
        return err;
    }

    //strcpy(dev, ifr.ifr_name); // this line leads to errors.

    return fd;
}


int main()
{
    int fd, nread;
    char buffer[1500];

    if ((fd = tun_alloc(NULL, IFF_TUN | IFF_NO_PI)) < 0) {
        printf("tun alloc failed!\n");
        exit(1);
    }

    while (1) {
        if ( (nread = read(fd, buffer, sizeof(buffer))) < 0) {
            printf("read failed!\n");
            close(fd);
            exit(1);
        }
        
        /* process packets here */
    }

    /* 程序关闭文件描述符、网络设备和所有相应的路线将会消失 */
    return 0;
}
```

### compile & run


```shell
$ gcc tun.c -o tun
$ ./tun

# open another window
$ ip addr add 10.0.0.1/24 dev tun0
$ ip link set tun0 up

# test packet acceptance
$ ping 10.0.0.2
PING 10.0.0.2 (10.0.0.2) 56(84) bytes of data.
64 bytes from 10.0.0.2: icmp_seq=1 ttl=64 time=0.024 ms
64 bytes from 10.0.0.2: icmp_seq=2 ttl=64 time=0.035 ms
...
```



## 参考

- *[Tun/Tap interface tutorial](https://backreference.org/2010/03/26/tuntap-interface-tutorial/)*

- *[Documentation/networking/tuntap.txt](https://www.kernel.org/doc/Documentation/networking/tuntap.txt)*
- *[net/tun.c](https://github.com/torvalds/linux/blob/v4.4/drivers/net/tun.c)*
