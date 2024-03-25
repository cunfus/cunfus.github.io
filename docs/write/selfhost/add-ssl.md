# self-hosted-using-ssl 

## 结构图



![](https://pic.imgdb.cn/item/64d0e1231ddac507cc915777.png)

## 基础点

1. 容器可以加入同一个网络中，并通过容器名相互通信
2. 命令行下程序与用户的交互可以用 expect 脚本自动化控制


## 步骤

### 1. 生成自签名证书

```shell
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365
```

### 2. 配置 nginx.conf 

指定证书，并设置代理转发

```shell
    server {
        listen       11443 ssl;
        server_name  localhost;

        ssl_certificate       /usr/local/nginx/certs/cert.pem;
        ssl_certificate_key   /usr/local/nginx/certs/key.pem;

        location / {
            proxy_set_header  Host              $http_host;
            proxy_set_header  X-Real-IP         $remote_addr;
            proxy_set_header  X-Forwarded-For   $proxy_add_x_forwarded_for;

            proxy_pass http://web:80;
        }
        # ......
    }
    # server { # 新增 Web 服务
    #    ...
    # }
```

### 3. expect 脚本

输入启动时的证书密码，这一步其实并不安全，完全是为了简化操作步骤。

```shell
# pem.expect
#!/usr/bin/expect

set timeout 30
set password "123456"

spawn /usr/local/nginx/sbin/nginx

expect {
    "PEM" { send "$password\r"; exp_continue } # 多个使用 SSL 的服务需要重复 send 多次
    eof
}
```

### 4. 编译生成 nginx 镜像 

也可以直接用 dockerhub 里现成的镜像，不过打算有空读读源码，先熟悉下过程

`docker build -t nginx-proxy .`

```dockerfile
FROM ubuntu
WORKDIR /code

# ubuntu 替换镜像源
RUN sed -i s@/archive.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list \
    && sed -i s@/security.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list \
    && apt-get clean && apt-get update -y

# 编译 nginx 的库 & 工具
RUN apt-get install gcc make libz-dev libpcre3-dev libssl-dev expect wget -y

# 编译 nginx
RUN wget http://nginx.org/download/nginx-1.24.0.tar.gz \
    && tar xf ./nginx-1.24.0.tar.gz && cd ./nginx-1.24.0 \
    && ./configure --prefix=/usr/local/nginx --with-http_ssl_module  \
    && make && make install
```

### 5. compose 部署

创建互联网络 `docker network create ngx-net`

```yaml
  version: '3'
  # 内部监听端口 80  
  web:
    image: python:3.7-alpine
    container_name: web
    restart: always
    networks:
      - ngx-net
    tty: true
```


~~~yaml
version: '3'

services:
  nginx:
    image: nginx-proxy:latest
    container_name: nginx-proxy
    networks:
      - ngx-net
    volumes:
      - ngx/certs:/usr/local/nginx/certs # 存放步骤1 pem 证书
      - ngx/conf:/usr/local/nginx/conf   # 存放步骤2 conf 文件
      - ngx/sbin:/usr/local/nginx/sbin   # 存放步骤3 expect 脚本
    ports:
      - 11443:11443
    tty: true
~~~

### 6. 启动
```bash
$ docker compose up -d
$ docker exec -it nginx-proxy expect pem.expect
```

## 总结

容器隔离了环境，复杂度一部分转移到了部署层面上。

自签名证书一般多用于自嗨，不属于正规流派，应用场景不大。

dockerfile 和 compose 还真是方便。
