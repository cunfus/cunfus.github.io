# Tmux

!!! quote
    鼠标正在拖慢你的速度！

按键约定：如 <C-a\> c 按键组合，需要先按下 Ctrl 和 a，松开后再按下 c。

命令前缀：如果程序是在 tmux 环境中运行的，需要一种方式告诉 tmux，某个操作是 tmux 去做而不是程序去做，这就是前缀键的作用。在这里，将前缀设置成 <C-a\>。

!!! tip "关于 Ctrl 键位"
    Windows 下可以修改注册表，交换 CapsLk 和 Ctrl。

    Manjaro 在 setting 里配置，交换 CapsLk 和 Ctrl。

    这样一来，<C-a\> 将是两个在物理位置上连续的按键。


## 基础用法

tmux 三个关键概念层层嵌套：会话-窗口-面板

- 会话：每个会话是一个独立的工作区，包含一个或多个窗口
    - `tmux` 启动一个新会话
    - `tmux ls` 列出所有会话
    - `tmux attach -t N` 登入序号为 N 的会话
    - `tmux a` 登入最后一个会话
    - `tmux kill-session -t N` 杀死序号为 N 的会话
- 窗口：每个窗口相当于浏览器中的标签页，可以分割成多份面板
    - `<C-a> w` 列出所有窗口
    - `<C-a> N` 跳转到第 N 个窗口
    - `<C-a> c` 创建一个新的窗口
    - `<C-a> ,` 重命名窗口
    - `<C-d>` 关闭窗口
    - `<C-a> <C-c>` 创建新会话
    - `<C-a> d` 临时登出当前会话
- 面板：面板将一个窗口分割为多个区域，每个面板是一个单独的交互式 shell 界面
    - `<C-a> -` 水平分割
    - `<C-a> _` 垂直分割
    - `<C-a> <方向>` 切换到指定面板
    - `<C-a> <C-方向>` 调整面板边界 
    - `<C-a> z` 缩放（zoom）/恢复 面板
    - `<C-a> [` 回滚屏幕 

## 结对编程

使用 tmux 结对编程的原理跟简单，只要两人登录到同一个会话就行了，两人将共享屏幕信息。

这样做的一个好处是，比起图形化桌面共享要节省不少网络带宽，因此低网速也能很好的工作。另一个好处来源于 tmux 自身，临时登出和再次登入的操作很方便。


## 配置文件

推荐用心制作的 [.tmux](https://github.com/gpakosz/.tmux)。在安装 tmux 后，直接使用这份配置能省心不少。

搭配使用，在 .tmux.conf 里将默认终端换成 zsh。

``` shell
set -g default-shell /bin/zsh
set -g default-command /bin/zsh
```

## 插件-会话恢复

通过 tmux 保留工作环境，帮助回忆思路。

1. 安装 tmux 插件管理器 [tpm](https://github.com/tmux-plugins/tpm)
2. 安装插件 [tmux-resurrect](https://github.com/tmux-plugins/tmux-resurrect)
3. 使用快捷键
    - `<C-a> <C-s>` 保存
    - `<C-a> <C-r>` 恢复



