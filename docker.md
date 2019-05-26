## docker

### 概念

配置文件 centos : /etc/sysconfig/docker

ubuntu : /etc/default/docker      /etc/systemd/system/multi-user.target.wants/docker

#### docker 组成

1. docker client
2. docker server

#### docker 组件

1. 镜像(只读)
2. 容器
3. 仓库

#### 镜像管理

1. 搜索镜像  docker search
2. 获取镜像  docker pull
3. 查看镜像 docker images
4. 删除镜像 docker rmi
5. 删除所有镜像  `docker rmi `docker images -q``

#### 运行

1. docker run --name mydocker -it centos /bin/bash

或者通过docker ps -a 查看容器信息, container id

2. docker start 容器id

3. 启动后台, docker run -d --name mydocker1 centos

#### 容器管理

1. 查看容器  docker ps -a  查看之前启动的容器
2. docker ps  当前运行的容器
3. 名字可以自动生成
4. docker 容器stop 后, 仍然会占用磁盘空间
5. 进入一个容器 docker attach 容器id, docker exec -it 容器name 或者容器id /bin/bash
6. 删除容器 docker rm
7. 获取容器的PID  `docker inspect --format "{{.State.Pid}}" 名称或者容器id`
8. 进入容器  `sudo nsenter --target pid号  --mount --uts --ipc --net --pid`
9. 删除所有容器  `docker  rm `docker ps -a -q``

#### 进入容器的脚本

```shell
#!/bin/bash
CNAME=$1
CPID=$(docker inspect --format "{{.State.Pid}}" $CNAME)
nsenter --target "$CPID" --mount --uts --ipc --net --pid
```

参数是容器名字或者id

#### 网络管理

1. brctl show

2. sudo iptables -L -n
3. sudo iptables -t nat -L -n

#### 端口映射

1. 随机映射 -P: 

`docker run -d -P --name mynginx1 nginx`

指定端口 -p:

`docker run -d -p 91:80 --name mynginx2 nginx`

* -p hostport:containerport
* -p ip:hostport:containerport
* -p ip::containerport
* -p hostport:containerport
* -p hostport:containerport

#### 数据管理

同步目录, 宿主机必须不存在

1. 数据卷 -v /data  -v src:dst

* `docker run -it --name valume-test1 -h nginx -v /data centos` :  将容器中的data 目录同步到主机上的一个目录. 随机生成的目录名字
* 使用 docker inspect 查看, 有一个Mounts 字段, 有Source信息
* `docker run -it --name valume-test3 -h nginx -v test:/soft` , 使用docker inspect 可以看到目录
* `docker run -it --name volume-test2 -h nginx -v /opt:/opt centos` 将宿主机上的目录同步到容器内
* 宿主机和容器内的对应文件夹UID 是相同的, 
* 如果指定了宿主机的目录  -v /test:/soft, 那么销毁容器, 宿主机上的/test 目录不会删除
* 如果没有制定宿主机目录  -v /soft, 那么销毁容器, 宿主机上随机分配的一个目录也不会被删除

2. 数据卷容器  --volumes-from

* docker rum -it --name valume-test4 --volumes-from valume-test1 centos
* 就是一个正常的容器, 专门用来提供数据卷供其他容器挂载的, 

#### 容器构建

1.  `docker commit -m "my java" 64b2e7b9454d qingqi/java:v1`

2. Dockerfile 基本镜像信息, 维护者信息, 镜像操作指令, 容器启动时执行指令

* FROM :(他的妈妈是谁)  基础镜像
* MAINTAINER: 维护者信息(谁创造了它)
* RUN : 把命令前面加上RUN 
* ADD : COPY 文件, 自动解压 (往它肚子里放点文件)
* WORKDIR: 当前工作目录: (CD)
* VOLUME 给我一个存放行李的地方 (目录挂载)
* EXPOSE 端口()
* RUN 进程一直运行下去

```
# This is my first Dockerfile
# Version 1.0
# Author: qingqi
# Base images
From centos

MAINTAINER qingqi

#ADD

ADD nginx /usr/local/src

#RUN

RUN yum install -y wget gcc gcc-c++ make openssl-devel
RUN useradd -a /sbin/nologin -M www

WORKDIR /usr/local/src

RUN touch myfile

RUN echo "daemon off;" >> /usr/local/myfile/conf/nginx.conf
ENV PATH /usr/local/nginx/sbin/:$PATH
EXPOSE 80
CMD ["nginx"]
```

`docker build -t nginx-file:v1 /opt/docker-file/nginx/`

#### 文件上传和下载

docker cp src container:dest

`docker cp hadoop-3.1.1.tar.gz Java-base:/`

docker cp Java-base:/hadoop-3.1.1.tar.gz /

#### docker 资源隔离 LXC Kernel namespace

cgroup CPU 内存 (磁盘 还不行)

docker run -c=  默认1024 (可以使用100%)

```dockerfile 
FROM centos
RUN yum -y install epel-release
RUN yum -y install stress
ENTRYPOINT ["stress"]
```

1. **cpu**

docker run -it --rm stress  --cpu 1` 默认 -c的数值是1024 

`docker run -it --rm -c 512 stress --cpu 1` 这个是指定了512 cpu share

如果有两个docker 容器, 一个cpu share 1024, 一个是512, 那么CPU会以 1:2 分配

`docker run -it --cpuset-cpus=0-7 --rm stress --cpu 8 ` 指定 cpu 的数量, 我的cpu 有8个

2. **内存**

`docker run -it --rm -m 128m stress  --vm 1 --vm-bytes 120m --vm-hang 0`

#### 网络和registry

ifconfig 有一个docker0, 默认情况下, 使用桥接网卡

brctl show

1. 启动 registry
2. 重新打标 `docker tag qingqi/hadoop:v1 localhost:5001/test/hadoop:v1`
3. docker push localhost:5001/test/hadoop:v1
4. 查看 远程仓库有哪些镜像:  `curl http://localhost:5001/v2/_catalog`

#### docker web

shipyard



