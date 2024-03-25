# Manual

man 命令，相当有用。

打个比方，在网络编程时，忘记了常用的 socketaddr 结构属于哪个头文件，这时候 `man socketaddr` 就会出现需要的信息。

`man man` 可以查询自身怎么使用

```
1   Executable programs or shell commands
2   System calls (functions provided by the kernel)
3   Library calls (functions within program libraries)
```

比如 

- `man 3` 调出的手册就是 Library Functions Manual
- `man 2` 调出的手册就是 System Calls Manual
- `man -k` 按照正则搜索关键字条目
- `man -f` 给出关键字的简要描述信息

一些可以通过 man 查到的

- `man ascii` ASCII 字符表
- `man hier` linux filesystem hierarchy

此外，除了 man，还有 apropos 和 info 用来查询手册信息。
