# Dockerfile Guide

## 指令总览

| 指令       | 说明                                                |
| ---------- | --------------------------------------------------- |
| FROM       | 设置基础镜像                                        |
| MAINTAINER | 设置镜像作者                                        |
| RUN        | 编译镜像时，运行的脚本                              |
| CMD        | 设置容器的启动命令                                  |
| LABEL      | 设置镜像的标签                                      |
| EXPOSE     | 设置暴露端口                                        |
| ENV        | 设置容器的环境变量                                  |
| ADD        | 编译镜像时，复制文件到镜像中                        |
| COPY       | 编译镜像时，复制文件到镜像中                        |
| ENTRYPOINT | 设置容器的入口程序                                  |
| VOLUME     | 设置容器的挂载卷                                    |
| USER       | 设置 RUN、CMD、ENTRYPOINT 指令的用户名              |
| WORKDIR    | 设置 RUN、CMD、ENTRYPOINT、COPY、ADD 指令的工作目录 |
| ARG        | 设置编译镜像时，加入的参数                          |
| ONBUILD    | 设置镜像的ONBUILD指令                               |
| STOPSIGNAL | 设置容器的退出信号量                                |

## 编译镜像 docker build

> 最简单编译过程
>
> > ```bash
> > docker build -t <image_name> .
> > # -t 为编译出的镜像设置 tag
> > ```
>
> Docker Daemon 默认使用缓存机制， --no-cache 不使用缓存





## .dockerignore 文件

> 编译目录中有 .dockerignore 文件，格式与.gitignore 一致
>
> ```bash
> */tmp* # 忽略一级子目录下所有以tmp开头的文件和目录
> */*/tmp* # 忽略二级子目录...
> tmp? # 
> ！README.md
> ```
>
> 

## FROM

```dockerfile
FROM <image>
FROM <image>:<tag> # 默认使用最新的
FROM <image>@<digest>
```



## MAINTAINER

```dockerfile
MAINTAINER <name>
```



## RUN

````dockerfile
RUN <command> # shell 方式， bash
RUN ["executable", "param1", "param2"] # exec 方式， 指定其他shell； 不能用单引号； 不能读取环境变量

exp:
RUN ["echo","$HOME"] #错误
RUN ["sh","-c","echo","$HOME"] #正确
````





## CMD

一个 Dockerfile 中只能有一个 CMD 命令；若有多条，则只有最后一条生效

```dockerfile
CMD ["executable", "param1", "param2"] # exec 方式
CMD ["param1","param2"] # ENTRYPOINT  参数方式
CMD command param1 param2 # shell 方式
```



## LABEL

镜像会继承基础镜像标签，LABEL指令可以覆盖基础镜像中的同名标签

```dockerfile
LABEL <key>=<value>

LABEL description="This text illustrates"
LABEL version="1.0"

# 每一个 LABEL 都会增加一层
LABEL multilabel1="value1" \
	multilabel2="value2" \
	multilabel3="value3"
```



## EXPOSE

```dockerfile
EXPOSE <port> [<port>...]
```

```bash
docker run -d -P ubuntu
-P 导出所有暴露端口，并随机分配一个主机端口
```



## ENV

```dockerfile
ENV <key> <value>
ENV <key>=<value> ...

# 每一个 ENV 都会增加一层
ENV NAME sinow
ENV COUNTRY China
ENV CITY Cheng du

ENV NAME sinow COUNTRY China CITY "Cheng du"
```





## ADD

```dockerfile
ADD <src>... <dest>
ADD ["<src>",... "<dest>"]

ADD hom* /mydir/
ADD hom?.txt /mydir/

ADD test relativeDir/ 复制编译目录 test 目录到'WORKDIR' /relativeDir 中
ADD test /absoluteDir/ 复制到 /absoluteDir 中

#src 可以时 文件、目录、或者URL，支持通配符
```





## COPY

```dockerfile
COPY <src>... <dest>
COPY ["<src>",..."dest"]

COPY hom* /mydir
COPY hom?.txt /mydir/

COPY test relativeDir/
COPY test /absoluteDir/

# src 可以时 文件、目录，支持通配符
# 在镜像中，目标文件和目录的UID、GID都是0
```





## ENTRYPOINT

在 Dockerfile 的最后加上下面这一行：
ENTRYPOINT ["/usr/games/cowsay"]
现在再次生成新镜像，以后使用这个新镜像时再也不需要指定 cowsay 命令了：

```bash
$ docker build -t test/cowsay-dockerfile . 
... 
$ docker run test/cowsay-dockerfile "Moo" 
...
```



## VOLUME

```dockerfile
VOLUME ["/data"]
VOLUME /data1 /data2
# volume 创建的挂载点，无法指定主机上对应的目录，是自动生成的


#test
FROM ubuntu
MAINTAINER hello1
VOLUME ["/data1","/data2"]

```





## USER

使用USER设置执行RUN、CMD和ENTRYPOINT的用户名或UID

````dockerfile
USER daemon
````





## WORKDIR

设置RUN、CMD、ENTRYPOINT、ADD、COPY指令的工作目录

````dockerfile
WORKDIR /path/to/workdir # 若不存在，则自动创建
````

```dockerfile
WORKDIR /a
WORKDIR b
WORKDIR c
RUN pwd
# pwd为 /a/b/c
```

```dockerfile
# 可使用ENV定义的环境变量

ENV DIRPATH /path
WORKDIR $DIRPATH/$DIRNAME
RUN pwd
# pwd为 /path/$DIRNAME
```



## ARG





## ONBUILD



## STOPSIGNAL

设置容器退出时，Docker Daemon 向容器发送的信号量

```dockerfile
STOPSIGNAL signal

STOPSIGNAL 9
STOPSIGNAL SIGKILL
```



