# tcp-ip-stack

The realm of network programming involves various concepts, which often overwhelm beginners. However, these concepts are foundational and essential. Unfortunately, in practical work, we often encounter them in isolated and fragmented ways, without a clear and gradual learning path. This can be quite disheartening.



## tcp/ip stack intro

```basic

  +-------+                +-------+                         +-------+
  | ping  |                |telnet |                         |  DNS  |
  +---+---+                +---+---+                         +---+---+
      |                        |                                 |
+----------------------------------------------------------------------+  socket
      |                        |                                 |
      |                    +---+---+                         +---+---+
      |                    |  TCP  +-------------+-----------+  UDP  |
      |                    +-------+             |           +-------+
      |                                          |
+----------------------------------------------------------------------+
      |                                          |
  +---+---+                                      |
  | ICMP  +--------------------------------------+
  +-------+                                      |
                                             +---+---+
                                             |  IP   |
                                             +---+---+
                                                 |
+----------------------------------------------------------------------+
                                                 |
                         +-------+               |
                         |  ARP  +---------------+
                         +-------+               |
                                             +---+---+
                                             | Frame |
                                             +-------+


```


### arp

```basic


ARP                                            <net/if_arp.h> struct arphdr
                     +----------------------+  ar_hdr  /* format of  hardware address */
                     |    +-----------------|  ar_pro  /* format of  protocol address */
                     |    |    +------------|  ar_hln  /* length of  hardware address */
                     |    |    |  +---------|  ar_pln  /* length of  protocol address */
                     |    |    |  |  +------+  ar_op   /* ARP opcode (command)        */
                     |    |    |  |  |
                     +----------------------------------------------------------------+
              (byte) |2   |2   |1 |1 |2   |6           |4       |6           |4       |
                     +----------------------------------------------------------------+
                                          |            |        |            |
<netinet/if_ether.h> struct ether_arp     |            |        |            |
arp_sha  /* sender hardware address */  +-+            |        |            |
arp_spa  /* sender protocol address */  |--------------+        |            |
arp_tha  /* target hardware address */  |-----------------------+            |
arp_tpa  /* target protocol address */  +------------------------------------+


```


### ip

```basic

IP
                        1                            1
(bit)  0  3 4  7 8      5            0      7 8      5
      +----+----+--------+          +-----------------+    <netinet/ip.h> struct ip
      | V  | hl | tos    |   -----> |ttl     |p       |
      +----+----+--------+  /       +-----------------+    ip_v    /* version               */
      ^                    /                               ip_hl   /* header length         */
      |                   |                                ip_tos  /* type of service       */
      +--------------+--------------------------------+    ip_len  /* total length          */
      |    |len |id  |    |    |sum |src     |dst     |    ip_id   /* identification        */
      +-------------------+---------------------------+    ip_off  /* fragment offset field */
(byte) 2    2    2   |2    2    2    4        4            ip_ttl  /* time to live          */
                     v                                     ip_p    /* protocol              */
                     +-----------------+                   ip_sum  /* checksum              */
                     |id |off          |                   ip_src  /* source address        */
                     +-----------------+                   ip_dst  /* dest address          */
                      0 2 3           1
                                      5


```

### icmp

```basic

ICMP                                       <netinet/ip_icmp.h>

+--------------------------------------->  type         /* message type  */
|  +------------------------------------>  code         /* type sub-code */
|  |  +--------------------------------->  checksum
|  |  |
+-----------+---------------------------+  union {
|1 |1 |2    | 4 (variable)              |    struct {
+-----------+---------------------------+      id
                                               sequence
 ICMP_ECHO      8  <--------------------+    } echo     /* echo datagram   */
 ICMP_ECHOREPLY 0           type
                                             gateway    /* gateway address */

                                             struct {
                                               reserved
                                               mtu
                                             } frag     /* path mtu discovery */
                                           }


```


### tcp

```basic

TCP                           1 1 1 1 1 1            <netinet/tcp.h>
        (bit)   0  3 4  7 8 9 0 1 2 3 4 5
               +-----------+-+-+-+-+-+-+-+           sport    off  :4    win
               |off |res1| | | | | | | | |           dport    res1 :4    sum
               +-----------+-+-+-+-+-+-+-+           seq      CWR  :1    urp
                                         ^           ack      ECE  :1
                                         |                    URG  :1
       +---------------------------+--+------------------+    ACK  :1
       |sp  |dp  |seq     |ack     |  |  |win |sum |urp  |    PSH  :1
       +---------------------------+--+------------------+    RST  :1
(byte)  2    2    4        4        1  1  2    2    2         SYN  :1
                                                              FIN  :1

```

### udp

```basic
UDP                               <netinet/udp.h>

       +------------------+       sport
       |sp  |dp  |ulen|sum|       dport
       +------------------+       ulen
(byte)  2    2    2    2          sum


```

### dns

```basic

DNS                                         Response      +---------+
                                            Opcode                  |
                                            Authoritative           |
                                         6  Truncated               |
                              1 1 1  1   7  Recursion desired       |
      (bit)  0 1  4 5 6 7 8 9 0 1 2  5      Recursion available     |
            +-+----+-+-+-+-+-+-+-+----+     Z                       |     transaction id
            | |    | | | | | | | |    |     Answer anthenticated    +---+ flags
            +-+----+-+-+-+-+-+-+-+----+     Non-authenticated data  |     quentions
            ^                               Reply code    +---------+     answer RRs
            |                                                             authority RRs
       +---------+----+----+----+----+----+-----+                         additional RRs
(byte) |    |    |    |    |    |    |    |    ||                         queries
       +----+----+----+----+----+----+----+-----+                         ansers
        2    2    2    2    2    2    2    2


```

## practice

network topology

```basic

+-------------------+                  +-------------------+
| laptop            |                  | pi                |
| 192.168.2.10      |                  | 192.168.2.2       |
| a8:9a:73:35:96:89 |                  | e4:5f:36:68:db:bd |
| manjaro           |                  | raspberry         |
+--------+----------+                  +---------+---------+
  wlp2s0 |                                       |   eth0
+--------+--------------------+------------------+---------+
                      wlan0   |
                   +----------+----------+
                   | Router              |
                   | 192.168.2.1         |
                   | 90:f7:f2:eb:bb:d4   |
                   +----------+----------+
                              |
                              +

```


### arp

```shell
$ sudo arp -d 192.168.2.2
$ sudo tcpdump -i wlp2s0 -ent '(dst 192.168.2.2 and src 192.168.2.10) or 
                               (dst 192.168.2.10 and src 192.168.2.2)' -w arp.pcap 

# another window
$ telnet 192.168.2.2 echo
Trying 192.168.2.2...
Connected to 192.168.2.2.
Escape character is '^]'.
hello!
hello!
^]
telnet> quit
Connection closed.

```
```basic
a8:9a:73:35:96:89 > ff:ff:ff:ff:ff:ff, ethertype ARP (0x0806), length 42: 
        Request who-has 192.168.2.2 tell 192.168.2.10, length 28
e4:5f:36:68:db:bd > a8:9a:73:35:96:89, ethertype ARP (0x0806), length 42: 
        Reply 192.168.2.2 is-at e4:5f:36:68:db:bd, length 28


```

### dns

```shell
$ sudo tcpdump -i eth0 -nt -s 500 port domain
IP 192.168.2.2.54537 > 192.168.2.1.53: 40868+ A? www.baidu.com. (31)
IP 192.168.2.1.53 > 192.168.2.2.54537: 40868 2/0/0 A 14.119.104.254, A 14.119.104.189 (63)

# another window
$ host -t A www.baidu.com
www.baidu.com has address 14.119.104.254
www.baidu.com has address 14.119.104.189

```

### icmp

MTU: 
The maximum transmission unit (MTU) of a frame.

IP Fragment: 
If the IP layer has data to transmit and the length of the data is larger than the maximum transmission unit (MTU) of the link layer, then the IP layer needs to perform fragmentation.

```basic
MTU:   20                    8              1472                        = 1500
      +---------------------+-------------+---------------------------+
      | IP hdr              | ICMP hdr    | ICMP data                 |
      +---------------------+-------------+---------------------------+

      if data > 1472  +------------+       +------------+       +------------+
                      | IP    hdr  |       | IP    hdr  |       | IP    hdr  |
      for example:    | (set  MF)  |       |(set offset)|       |(set offset)|
                      +------------+       +------------+       +------------+
                      | ICMP  hdr  |   +   |            |   +   |            |
                      +------------+       |            |       |            |
                      | ICMP  data |       | ICMP  data |       | ICMP  data |
                      |   1472     |       |   1480     |       |     1      |
                      +------------+       +------------+       +------------+
                       1500                 1500                 21


```

```basic
$ ping 192.168.2.2
$ sudo tcpdump -i wlp2s0 -ntv icmp 
IP (tos 0x0, ttl 64, id 13535, offset 0, flags [DF], proto ICMP (1), length 84)
    192.168.2.10 > 192.168.2.2: ICMP echo request, id 12, seq 1, length 64
IP (tos 0x0, ttl 64, id 11628, offset 0, flags [none], proto ICMP (1), length 84) 
    192.168.2.2 > 192.168.2.10: ICMP echo reply, id 12, seq 1, length 64 

IP (tos 0x0, ttl 64, id 13681, offset 0, flags [DF], proto ICMP (1), length 84) 
    192.168.2.10 > 192.168.2.2: ICMP echo request, id 12, seq 2, length 64 
IP (tos 0x0, ttl 64, id 11827, offset 0, flags [none], proto ICMP (1), length 84)
    192.168.2.2 > 192.168.2.10: ICMP echo reply, id 12, seq 2, length 64


$ ping 192.168.2.2 -s 1473
$ sudo tcpdump -i wlp2s0 -ntv icmp 
IP (tos 0x0, ttl 64, id 21704, offset 0, flags [+], proto ICMP (1), length 1500) 
    192.168.2.10 > 192.168.2.2: ICMP echo request, id 13, seq 1, length 1480 
IP (tos 0x0, ttl 64, id 21704, offset 1480, flags [none], proto ICMP (1), length 21) 
    192.168.2.10 > 192.168.2.2: ip-proto-1 
IP (tos 0x0, ttl 64, id 25541, offset 0, flags [+], proto ICMP (1), length 1500) 
    192.168.2.2 > 192.168.2.10: ICMP echo reply, id 13, seq 1, length 1480 
IP (tos 0x0, ttl 64, id 25541, offset 1480, flags [none], proto ICMP (1), length 21) 
    192.168.2.2 > 192.168.2.10: ip-proto-1 

```


### tcp & ip 

#### tcp

tcpdump flags: S (SYN), F (FIN), P (PUSH), R (RST), U (URG), W (ECN CWR), E (ECN-Echo) or '.' (ACK), or 'none' if no flags are set.


```basic
$ sudo tcpdump -i wlp2s0 -nt '(dst 192.168.2.2 and src 192.168.2.10) or 
                              (dst 192.168.2.10 and src 192.168.2.2)'


IP 192.168.2.10.53014 > 192.168.2.2.7: Flags [S], seq 334370016, win 64240, 
        options [mss 1460,sackOK,TS val 3258289009 ecr 0,nop,wscale 7], length 0
IP 192.168.2.2.7 > 192.168.2.10.53014: Flags [S.], seq 2665287474, ack 334370017, win 65160, 
        options [mss 1460,sackOK,TS val 1146990448 ecr 3258289009,nop,wscale 7], length 0
IP 192.168.2.10.53014 > 192.168.2.2.7: Flags [.], ack 1, win 502, 
        options [nop,nop,TS val 3258289143 ecr 1146990448], length 0       


IP 192.168.2.10.53014 > 192.168.2.2.7: Flags [P.], seq 1:9, ack 1, win 502, 
        options [nop,nop,TS val 3258293902 ecr 1146990448], length 8       
IP 192.168.2.2.7 > 192.168.2.10.53014: Flags [.], ack 9, win 509, 
        options [nop,nop,TS val 1146995364 ecr 3258293902], length 0   
IP 192.168.2.2.7 > 192.168.2.10.53014: Flags [P.], seq 1:9, ack 9, win 509, 
        options [nop,nop,TS val 1146995365 ecr 3258293902], length 8
IP 192.168.2.10.53014 > 192.168.2.2.7: Flags [.], ack 9, win 502, 
        options [nop,nop,TS val 3258294059 ecr 1146995365], length 0


IP 192.168.2.10.53014 > 192.168.2.2.7: Flags [F.], seq 9, ack 9, win 502, 
        options [nop,nop,TS val 3258305051 ecr 1146995365], length 0
IP 192.168.2.2.7 > 192.168.2.10.53014: Flags [F.], seq 9, ack 10, win 509, 
        options [nop,nop,TS val 1147006418 ecr 3258305051], length 0
IP 192.168.2.10.53014 > 192.168.2.2.7: Flags [.], ack 10, win 502, 
        options [nop,nop,TS val 3258305095 ecr 1147006418], length 0

```

```basic
# RST when accessing a non-existent port. 
IP 192.168.2.10.46498 > 192.168.2.2.54321: Flags [S], seq 2546188616, win 64240, 
        options [mss 1460,sackOK,TS val 3265806521 ecr 0,nop,wscale 7], length 0
IP 192.168.2.2.54321 > 192.168.2.10.46498: Flags [R.], seq 0, ack 2546188617, win 0, length 0
```

## thoughts

After gaining a fundamental understanding of the TCP/IP protocol model, let's proceed to explore sockets.




## refer

- *High Performance Linux Service Programming - chapter 1~3*
- *TCP/IP Illustrated Volume 1*
- *TCP/IP Illustrated Volume 2*
- *[stackoverflow - enable telnet server](https://stackoverflow.com/questions/34389620/telnet-unable-to-connect-to-remote-host-connection-refused)*

