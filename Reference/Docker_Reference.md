## Docker 常用指令

```bash
docker run -i -t debian /bin/bash
# 进入容器中的shell

docker run -h CONTAINER -i -t debian /bin/bash
# 为容器制定 hostname

docker ps
# 显示当前运行中的容器

docker ps -a
# 显示所有容器，包括已停止的容器

docker ps -qa
# 显示所有容器的 id

docker ps -qa -f status=exited
# 列出状态为 exited 的容器id

docker rm awesome_kapitsa
# 删除容器

docker rm `docker ps -qa`
docekr rm $(docker ps -qa)
# 删除所有容器

docker stop awesome_kapitsa
# 停止容器

docker inspect awesome_kapitsa
docker inspect 92bb2e7664ed
# 获取关于某个容器的详细信息
# id 可以仅输入一部分，最短是要在当前所有容器中唯一标识

docker inspect stupefied_turing | grep IPAddress 
docker inspect ––format {{.NetworkSettings.IPAddress}} stupefied_turing
# 过滤容器信息

docker diff eloquent_pare
# 查看容器改动情况

docker run --name batman debian echo "Hello"
# --name 指定容器名称

docker logs eloquent_pare
# 查看容器发生的一切事情

docker commit cowsay test/cowsayimage
# 将容器转成镜像

dockr run --name myredis -d redis
# -d 容器后台运行

docker run -it --link myredis:redis redis /bin/bash
# --link myredis:redis
# 把新容器与现存的 myredis 容器连接起来，并且在新容器中 以 redis 作为 myredis 容器的主机名
# 实现方法是：在 新容器中添加一个新的条目，把 redis 指向 myredis IP 地址
# 这样在redis-cli 时直接使用 redis 作为主机名即可，不需要输入 IP 地址


docker pull nginx
# 下载镜像


docker run -d -p 8000:80 nginx
# 将 主机的 8000端口 转发到 容器的 80号端口
docker run -d -P nginx
# Docker 自动选择一个主机上未使用的端口

docker port optinistic_easley 80
# 输出容器 80 号端口与主机的绑定关系

docker run -d -P --name port-test identidock
# Dockerfile EXPOSE 暴露的端口， -P 自动绑定到主机可用端口
docker port port-test
9090/tcp -> 0.0.0.0:49154
9090/tcp -> :::49154
9191/tcp -> 0.0.0.0:49153
9191/tcp -> :::49153
# 输出容器端口绑定关系


docker run -e "ENV=DEV" -p 5000:5000 identidock
# -e 设置环境变量


docker exec -it mynginx ls
# 在正在运行的容器中执行命令


# 以下为一些用例
docker run -it --name cowsay --hostname cowsay debian bash
docker run -d -p 5000:5000 -v "$PWD"/app:/app identidock
#-v "$PWD"/app:/app 参数把位于 /app 的 app 目录挂载到容器内。它将覆盖容器中 /app 目录的内容 ，而且在容器内还可以进行读写（如果你不希望这样，也可以把数据卷挂载为只读）。
```



### 数据卷 volume

数据卷时直接在主机挂载的文件或目录，允许多个容器共享

使用方法

1. 在 Dockerfile 里 使用 VOLUME 指令

   ```dockerfile
   FROM debian:wheezy 
   RUN useradd foo 
   RUN mkdir /data && touch /data/x 
   RUN chown -R foo:foo /data 
   VOLUME /data
   # 如果由于某些原因，你不能在 RUN 指令中设置卷的权限和它的所有者，那么就需要通过使用 CMD 或 ENTRYPOINT 指令，在容器创建后，以执行脚本的方式来解决。
   ```

2. docker run 时 使用 -v 参数

   ```bash
   docker run -v /data test/webserver
   # 默认情况下，volume 会挂载到主机的 Docker Root Dir
   # 容器中的 /data 目录便成为了一个数据卷。
   # 镜像的 /data 目录中的所有文件将被复制到数据卷内。
   # docker inspect -f {{.Mounts}} test/webserver
   # 可以获取 volume 在主机上的实际位置
   
   
   docker run -d -v /host/dir:/container/dir test/webserver
   # 绑定挂载
   # 指定挂载到容器的位置
   # 如果容器已经有 /data 目录，它的内容将会被数据卷所隐藏
   # 与先前的几个命令不同，这次并没有文件会从镜像复制到数据卷，而卷也不会被Docker 删除（换句话说，假如数据卷是挂载到一个用户选定的目录的话，那么 docker rm -v是不会把它删除的）。
   ```

- 当使用这个镜像来启动一个容器的时候，Docker 会把那个数据卷目录内的所有文件从镜像复制到容器。但如果数据卷是和主机目录绑定的话，那么镜像的数据就不会复制到容器了（这是为了防止主机文件被意外覆写）。

- 为容器备份

  ```bash
  $ docker run --rm -it --link myredis:redis redis /bin/bash 
  root@09a1c4abf81f:/data# redis-cli -h redis -p 6379 
  redis:6379> set "persistence" "test" 
  OK 
  redis:6379> save 
  OK 
  redis:6379> exit 
  root@09a1c4abf81f:/data# exit 
  exit 
  $ docker run --rm --volumes-from myredis -v $(pwd)/backup:/backup \ 
           debian cp /data/dump.rdb /backup/ 
  $ ls backup 
  dump.rdb
  ```

  ```bash
  docker rm -v myredis
  # -v, --volumes   Remove anonymous volumes associated with the container
  ```

  

### 共享数据

```bash
docker run -it -h NEWCONTAINER --volumes-from container-test debian /bin/bash
# 与卷关联的那个容器（在刚才的例子中，就是 container-test）无论正在运行与否，刚才的命令都能使用。只要至少存在着一个容器与卷关联，那么卷就不会被删除。
```

### 数据容器

- 唯一目的就是与其他容器分享数据

- ```bash
  docker run --name dbdata postgres echo "Data-Only"
  # dbdata 这个容器 echo 之后会 exited
  # 没有必要让数据容器一直运行
  
  docker run -d --volumes-from dbdata --name db1 postgres
  # 使用 dbdata 这个数据容器
  ```

- 数据卷只会在以下条件被删除

  - 容器被 docker rm -v 命令删除，或
  - docker run 命令执行时带有 --rm 选项
  - 目前没有容器与该数据卷关联
  - 该数据卷没有被指定使用主机目录（即没有使用 -v HOST_DIR:CONTAINER_DIR 语法）





### Dockerfile 设置USER

```dockerfile
FROM python
RUN groupadd -r uwsgi &&useradd -r -g uwsgi uwsgi
# 增添用户和组
RUN pip install Flask uWSGI
WORKDIR /app
COPY app /app

EXPOSE 9090 9191
USER uwsgi
# 指定 user

CMD ["uwsgi","--http", "0.0.0.0:9090", "--wsgi-file", "/app/identidock.py","--callable", "app","--stats", "0.0.0.0:9191"]

```





### 为容器添加网络功能

- 在宿主机上创建网桥设备 br0。
- 分配 IP 地址 192.168.1.254。

- 在容器内部创建一个网络接口，并为其分配 IP 地址 192.168.1.10。

- 最后在容器内部添加路由信息，将网桥设置为默认网关。



```bash
brctl addbr br0 #添加网桥设备 br0
ip addr add 192.168.1.254/24 dev br0 #为br0分配IP地址
ip link set dev br0 up # 启用网桥设备


ip link add foo type veth peer name bar
# 创建 veth 对，foo 和 bar
brctl addif br0 foo
# The  command 'brctl addif <brname> <ifname>' will make the interface <ifname> a port of the bridge <brname>. This means that all frames received  on  <ifname>  will  be  processed as if destined for the bridge. Also, when sending frames on <brname>,  <ifname>  will  be considered as a potential output interface.

ip link set foo up # 启用 foo


容器的网络在特定的网络命名空间中，需要获取其网络命名空间ID
docker inspect 中的SandboxKey为 /usr/run/docker/netns/$ID

ip link set bar netns $ID # 将 bar 连接到容器的网络命名空间

ip netns exec $ID ip link set dev bar name eth1 
# 将bar 命名为 eth1
ip netns exec $ID ip link set eth1 address 12:34:56:78:9a:bc
# 为 eth1 分配 MAC 地址
ip netns exec $ID ip link set eth1 up 
# 启用 eth1

ip netns exec $ID ip addr add 192.168.1.1/24 dev eth1 
# 为 eth1 分配 ip 地址
# 与 br0 处于同一子网

ip netns exec $ID ip route add default via 192.168.1.254
# 将 br0 作为 默认网关

iptables -t -nat -A POSTROUTING -s 192.168.0.0/24 -j MASQUERADE
# 添加 IP NAT 地址伪装规则

```

### 尝试手动配置（未完成）

```bash
[Arch run]# ln -s /var/run/docker/netns/ /var/run/netns
[Arch run]# brctl addbr br0
[Arch run]# ip addr add 192.168.2.254/24 dev br0
[Arch run]# ip link set dev br0 up
[Arch run]# ip link add foo type veth peer name bar
[Arch run]# brctl addif br0 foo
[Arch run]# ip link set foo up
[Arch run]# ip netns ls
e95b4421f22e
[Arch run]# ip link set bar netns e95b4421f22e
[Arch run]# ip netns exec e95b4421f22e ip link set dev bar name eth1
[Arch run]# ip netns exec e95b4421f22e ip link set eth1 address 12:34:56:78:9a:bc
[Arch run]# ip netns exec e95b4421f22e ip link set eth1 up
[Arch run]# ip netns exec e95b4421f22e ip addr add 192.168.2.1/24 dev eth1
[Arch run]# ip netns exec e95b4421f22e ip route add default via 192.168.2.254
[Arch run]# iptables -t nat -A POSTROUTING -s 192.168.2.0/24 -j MASQUERADE
```

