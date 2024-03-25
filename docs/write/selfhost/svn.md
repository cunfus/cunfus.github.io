# svn

## 简介

SVN 服务属于 IO 密集型任务。且相比于 Git，更适合于频繁进行大型二进制文件的存取。其内存占用极低，从自搭建情况看，内存使用一般在 3MB 左右，IO 峰值传输 20MB 左右（数据可能受限于硬件和带宽限制）。

## Server with docker

1. 安装服务 `docker compose up -d`

```yaml
# docker-compose.yaml
version: '3.3'
services:
	svn-server:
  	image: garethflowers/svn-server
  	container_name: svn-server
  	volumes:
      - '/svn:/var/opt/svn'
  	ports:
      - '3690:3690'
```

2. 生成仓库 `docker exec -it svn-server svnadmin create repo`

3. 配置 仓库账户文件位于 `^/repo/conf/{svnserver.conf passwd authz}`

```shell
# svnserver.conf
anon-access = none     # 非注册用户权限
auth-access = write    # 注册用户权限
password-db = passwd   # 密码文件路径
authz-db = authz       # 权限文件路径
```

```shell
# passwd
user = password        # 填写用户名密码注册
```

```shell
# authz
[repo:/path]
user = rw              # 设置用户名拥有的仓库路径权限
```

## Client

1. 安装客户端 `sudo pacman -S subversion`
2. 拉取服务端仓库 `svn checkout svn://path`
3. 其余命令用到再查 `svn -h` 


