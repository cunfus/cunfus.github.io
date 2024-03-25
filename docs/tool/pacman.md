# Pacman

pacman 是 arch 系 linux 的软件包管理工具，类似 redhat 系的 yum，debian 系的 apt。

这里记录下常用的操作：

- 安装
    - `pacman -S packet-name` 安装软件

- 更新
    - `pacman -Syu` 升级系统和所有已安装软件

- 卸载
    - `pacman -Rs packet-name` 卸载软件及其依赖

- 搜索
    - `pacman -Q  packet-name` 查询软件包版本 
    - `pacman -Qi packet-name` 查询软件包详细信息
    - `pacman -Ql packet-name` 列出软件包所有文件安装位置
    - `pacman -Qo filename` 查询文件属于哪个已安装的软件包
    - `pacman -Qs keyword` 搜索已安装的软件包
    - `pacman -Ss keyword` 仓库中搜索软件包

yay 是 AUR 的助手，可以执行 pacman 的几乎所有操作。
