# Rust App

## 1. 简介

Environment for **Rust** Appication

为运行 **Rust** 应用而提供的环境

## 2. 特性

1. Alpine
2. TZ=Asia/Shanghai
3. C.UTF-8
4. curl和telnet
5. 运行的jar包：/usr/local/vertx/myservice.jar

## 3. 编译并上传镜像

```sh
docker buildx build --platform linux/arm64,linux/amd64 -t nnzbz/rust-app:1.0.0 . --push
docker buildx build --platform linux/arm64,linux/amd64 -t nnzbz/rust-app:latest . --push
```

## 4. 单机

```sh
docker run -d --net=host --name 容器名称 --init -v /usr/local/外部程序所在目录:/usr/local/vertx --restart=always nnzbz/rust-app:latest:<版本>
```

## 5. Swarm

- Docker Compose

```sh
mkdir -p /usr/local/myapp/
vi /usr/local/myapp/stack.yml
```

```yaml{.line-numbers}
services:
  svr:
    image: nnzbz/rust-app:latest
    init: true
    hostname: myapp
    environment:
      # 日志级别
      - RUST_LOG=debug
      # 打印堆栈，如果全部打印设置为full
      - RUST_BACKTRACE=1
    volumes:
      # 初始化脚本
      #- ~/opt/myapp/init.sh:/usr/local/myapp/init.sh:z
      # 应用目录
      - ~/opt/myapp/app/myapp:/usr/local/myapp/myapp:z
      - ~/opt/myapp/app/myapp.yml:/usr/local/myapp/myapp.yml:z
    deploy:
      mode: global
      placement:
        constraints:
          # 部署的节点指定是app角色的
          - node.labels.role==app
    logging:
      options:
        max-size: 8m

networks:
  default:
    external: true
    name: rebue
```

- 部署

```sh
docker stack deploy -c /usr/local/myapp/stack.yml myapp
```
