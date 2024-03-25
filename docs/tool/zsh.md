# Zsh

!!! abstract
    介绍一些 shell 环境下常用的操作，和 zsh 的搭配用法。

## 1. 命令行垃圾桶

用惯了 Windows 的人，刚接触 Linux 时，印象最深刻的事情中估计有这么一条，rm 删除文件后，无法恢复。

这时候安装一份 [trash-cli](https://github.com/andreafrancia/trash-cli)，并重命名下 rm 或许会好得多。

``` shell
# ~/.zshrc
alias rm="trash"
alias rr="trash-restore"
```

## 2. 配置框架

[ohmyzsh](https://github.com/ohmyzsh/ohmyzsh) 用来管理 zsh 配置，它有丰富的插件，包括 emmm，总之很多。

可以从 [zsh 插件列表](https://github.com/ohmyzsh/ohmyzsh/wiki/Plugins) 中装配适合自己工作流的插件。

### 2.1 命令补全

终端下谁能减少重复敲命令的次数？

zsh 的 [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions) 插件可以根据历史记录补全命令，只需要敲出前几个字母，就会匹配到最近一次相匹配的命令。

除了前缀查找，大多数 Shell 支持子串查找，使用 Ctrl + r ，接着输入子串，回车执行。

### 2.2 目录跳转

终端下谁能避免记忆又长又臭的路径？

zsh 的 [autojump](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/autojump) 插件可以根据历史频率跳转到路径，只需要敲出几个字母，就会匹配到去过频率最高的路径下。


## 3. 目录浏览

[ranger](https://github.com/ranger/ranger) 这个命令还没用熟......


## 4. 远程连接

终端下最常见的操作就是远程到服务器，一次次输入密码既不安全，又让人比较头疼。

使用 ssh-keygen 生成私钥和公钥，登记私钥到本地的 ssh 配置文件里，发送公钥到远程服务器（需要输入一次密码），这样后面再连接远程服务器时，就做到了免密登陆。

操作步骤如下：

```shell
# 1. 生成私钥和公钥
$ ssh-keygen -t rsa -f ecs

# 2. 私钥登记到本地 ssh 配置文件
$ vim ~/.ssh/config

Host ecs
    HostName xx.xx.xx.xx
    User user-name
        IdentityFile ~/.ssh/server/ecs

Host ecs2
    HostName xx.xx.xx.xx
    User user-name
        IdentityFile ~/.ssh/server/ecs2

# 3. 发送公钥到远程服务器
$ ssh-copy-id -i ecs.pub  user-name@xx.xx.xx.xx

# 4. 免密连接
$ ssh ecs
```

## 5. 远程同步

远程连接到测试服务器后，开发环境的资料常常需要同步上去。rsync 支持增量备份，这一点对程序开发极为友善。

```shell
$ rsync -av src ecs:/code/

# -a             递归同步文件元信息
# -v             传输日志显示到终端
# -n             模拟执行
# --delete       同步时，远程将删除本地中没有的文件
# --link-desk    指定基准目录，然后增量备份
# --exclude=path 同步时排除 path 文件夹
# ...
```

rsync 可以本地到本地同步，本地和远程之间同步，但远程到远程之间需要一个触发机制。

正好，Linux 内核有 inotify 功能，可以监视文件系统的更改。而且 inotify-tools 中的 inotifywait 和 inotifywatch 可以直接用在 shell 脚本里。

这样，借助 inotify 这个触发器，rsync 也能够在远程与远程之间同步了。



