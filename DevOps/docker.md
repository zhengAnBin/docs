---
title: Docker
---

`docker run`
启动镜像

`docker build`
打包镜像

`docker rm`
删除容器

`docker rmi`
删除镜像

`docker stop`
停止容器

`docker start`
启动容器

`docker restart`
重启容器

`docker logs`
查看容器日志

`docker ps`
查看当前运行正常的容器

`docker exec`
进入容器内部

## docker run

docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

`--name`
指定容器的名字

`-d`
容器在后台运行

`-i`
打开 STDIN

`-t`
进入到容器内部

`-v`
给容器挂载存储卷，挂载到容器的某个目录（文件映射）

[🤏](https://www.cnblogs.com/yfalcon/p/9044246.html)

## Dockerfile

```Dockerfile
# 设置镜像使用的基础镜像
FROM node:14.17.4-alpine AS base

# 暴露端口
EXPOSE 3001

# 声明变量
ENV APP_PATH=/opt/node/app

# 设置工作目录
WORKDIR $APP_PATH

# 安装依赖阶段
FROM base As install

# 拷贝 package.json 到工作跟目录下
COPY package.json .

# 安装依赖 只安装dependencies依赖
RUN npm install --registry=https://registry.npm.taobao.org --production

# 最终阶段
FROM base

# 拷贝 装依赖阶段 生成的 node_modules 文件夹到工作目录下
COPY --from=install $APP_PATH/node_modules ./node_modules

# 把当前目录下的所有文件拷贝到镜像的工作目录下 .dockerignore 指定的文件不会拷贝
COPY . .

# 启动命令
CMD npm run start:prod
```

## docker-compose

## 一些用法记录

```docker
// 进入容器内部
// bash 和 sh 是两种不同 Linux Shell、有的容器两种都有，有的只有一种
docker exec -it conatinerId [/bin/bash or /bin/sh]
```
