# Docker初级入门

写在前面的,这是根据自学的一些散装的心得和要素。因为是便于自己理解的，所以逻辑上可能会出现不是很清晰的问题。

初级入门,不包含 Docker-compose 知识.

## 1. Docker是什么？三要素是什么？

开始学习`Docker`恶补一下。

在docker库里有很多很多app，我们本地拉去image，实例化就成了container。

**镜像 Image** --模板 （类比成月饼模子，面向对象里面的class类）不可写 只能读。image就是一个只读的模板

**容器 Container** --容器（上面镜像实例化出来的玩意儿 可读可写

**仓库 Repository ** --仓库就是放了一堆镜像的大库。

`docker run hello-world` 其实就是docker run 镜像，一切的开始。

是docker测试用的默认镜像，没有这个hello-world镜像就会自己从库自己拉取。

```shell
docker version
docker info # 更详细的详情
docker --help
```

看到一个蛮好的比喻。

就是Docker那个标志。大概就是宿主机，也就是我们用的原本的计算机。

鲸鱼就是Docker。

身上背着一堆集装箱就是容器CONTAINER实例。这些实例就是来自IMAGES。

![Docker-Compose を使ってコンテナ管理を簡単にする | エクセルソフト ブログ](https://www.xlsoft.com/jp/blog/wp-content/uploads/2017/01/large_v-trans.png)

![Docker Architecture and its Components for Beginner - Geekflare](https://geekflare.com/wp-content/uploads/2019/09/docker-architecture-609x270.png)

##### 列出本地所有images

```shell
docker images
# 表示仓库源         标签（不同版本）z       ID									创建时间							大小
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
vue_app_image       latest              3eb9c726a802        3 weeks ago         343MB
<none>              <none>              3c674c10599a        3 weeks ago         343MB
node                latest              a511eb5c14ec        3 months ago        941MB
<none>              <none>              f75465f182bd        6 months ago        343MB
nginx               latest              2073e0bcb60e        6 months ago        127MB
node                12.12.0-alpine      0fcfd7e52b09        10 months ago       80.9MB
hello-world         latest              fce289e99eb9        20 months ago       1.84kB
```

##### docker search 镜像名 可以查找所有镜像

##### docker rmi 镜像名

比如 docker rmi hello-world 删除镜像hello-world

![](https://raw.githubusercontent.com/chihokyo/image_host/master/20200827223250.png)

##### ps 查询宿主机的进程

##### docker ps 查询现在正在运行docker的进程

##### docker run --it --name 写别名 写镜像 

同一个本地镜像你可以启动多个容器。使用docker ps可以查看启动情况。

##### docker restart 容器ID 这个是重启容器的

很重要！

> exit 是完全退出 前× 后×
>
> ctrl + P+Q 前台○  后台×
>

如果想进去就要用2种方法。attach直接进去。docker exec 这样可以不直接进去，但是给了一个命令可以用来打印。隔山打牛。

docker images 没有的话就用 docker pull 你想要的镜像名 docker pull centos

docker run -it centos 从centos这个镜像开启一个新的容器，这个容器名字是随机的。

> 插播一个小知识
>
> 什么是tty
>
> `tty -- return user's terminal name`
>
> 就是你现在终端的名字，开俩窗口就俩名字。使用tty确认一下之后。用
>
> `echo 'I an ttys0000' >> /dev/ttys002` 就可以给另一个窗口发送消息

```shell
# 列出所有本地镜像
docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
centos              latest              0d120b6ccaa8        2 weeks ago         215MB
# 根据镜像ID新建一个容器CONTAINER并交互式运行在终端
docker run -it --name mycentos 0d120b6ccaa8
# 不知道选项是什么意思可以使用 docker run --help
# -t的意思是  -t, --tty  Allocate a pseudo-TTY 【伪终端显示】
# -i的意思是  -i, --interactive   Keep STDIN open even if not attached 【交互式】
[root@8a661224ba0b /]# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 07:24 pts/0    00:00:00 /bin/bash
root        14     1  0 07:29 pts/0    00:00:00 ps -ef
# 使用了俩命令 ，第一个是使用  /bin/bash 进入了这个窗口
docker ps
docker ps -l   最后上一次运行的容器
docker ps -n 3 最后上一次运行的数字容器 
docker ps -a   历史上所有容器
docker ps -q   只显示容器编号，模式 --quiet  Only display numeric IDs
docker ps $(qa) 可以用来批量删除容器
exit 完全退出 毫无留恋
ctrl + p + q 后台运行，现在终端退出 重新进去呢？
# 启动容器
docker start 容器id或者名字
docker restart 容器id或者名字
# 停止 类似于正常关机
docker stop 容器id或者名字
# 强制停止 类似于拔掉电源
docker kill  容器id或者名字
# 删除已经停止的容器
docker rm 容器id或者名字
# 删除还在运行的容器
docker rm -f 容器id或者名字
# 一次性删除多个容器
docker rm -f $(docker ps -aq)
docker ps -aq | xargs docker rm
# 一次性删除所有镜像
docker rmi $(docker images -q)
```

这里增加一个小玩意儿

```shell
➜  /Users/Chihokyo docker run -d 0d120b6ccaa8
8cf2267bae193625246d692ae466bcc4e7be2da158f825db65233a55389a82ef
➜  /Users/Chihokyo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
➜  /Users/Chihokyo docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS                          PORTS                    NAMES
8cf2267bae19        0d120b6ccaa8        "/bin/bash"              About a minute ago   Exited (0) About a minute ago
# 发现还是没有
# 虽然启动成功，但是发现并没有在运行。
# 只要不是一直挂起的命令，都是会自动退出的
# 来看查看日志
docker logs -f -t --tail 容器ID
docker inspect 容器ID 查看详情
```

如果是后台进行的容器现在想进入交互了怎么办

直接进入交互 `docker attach 1a326f7527c4`　这个并不常用 

` docker exec -it  1a326f7527c4 /bin/bash`完全可以用这个代替

```shell
➜  /Users/Chihokyo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
68bd17476d7c        centos              "/bin/bash"         14 seconds ago      Up 14 seconds                           mycentos
➜  /Users/Chihokyo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
1a326f7527c4        centos              "/bin/bash"         10 minutes ago      Up 10 minutes                           mycentos
➜  /Users/Chihokyo docker attach 1a326f7527c4
[root@1a326f7527c4 /]# read escape sequence
➜  /Users/Chihokyo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
1a326f7527c4        centos              "/bin/bash"         10 minutes ago      Up 10 minutes                           mycentos
➜  /Users/Chihokyo docker exec -t  1a326f7527c4 ls -l /tmp # 这样并没有交互
total 8
-rwx------ 1 root root  671 Aug  9 21:40 ks-script-2n9owwnh
-rwx------ 1 root root 1379 Aug  9 21:40 ks-script-xm1o5azb
➜  /Users/Chihokyo docker exec -it  1a326f7527c4 /bin/bash
[root@1a326f7527c4 /]#

```

## 2.如何复制容器里的文件到宿主机？

关于如何复制容器里的文件到宿主机

`docker cp 1a326f7527c4:/tmp/ks-script-xm1o5azb /Users/Chihokyo`

`docker cp 容器:容器绝对路径 宿主机绝对路径`

```shell
➜  /Users/Chihokyo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
1a326f7527c4        centos              "/bin/bash"         10 minutes ago      Up 10 minutes                           mycentos
➜  /Users/Chihokyo docker exec -t  1a326f7527c4 ls -l /tmp
total 8
-rwx------ 1 root root  671 Aug  9 21:40 ks-script-2n9owwnh
-rwx------ 1 root root 1379 Aug  9 21:40 ks-script-xm1o5azb
➜  /Users/Chihokyo docker exec -t  1a326f7527c4 /bin/bash
[root@1a326f7527c4 /]#

^C
➜  /Users/Chihokyo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
1a326f7527c4        centos              "/bin/bash"         25 minutes ago      Up 25 minutes                           mycentos
➜  /Users/Chihokyo docker exec -it 1a326f7527c4 ls -l /tmp
total 8
-rwx------ 1 root root  671 Aug  9 21:40 ks-script-2n9owwnh
-rwx------ 1 root root 1379 Aug  9 21:40 ks-script-xm1o5azb
➜  /Users/Chihokyo docker cp 1a326f7527c4:/tmp/ks-script-xm1o5azb /Users/Chihokyo
➜  /Users/Chihokyo ls ks*
ks-script-xm1o5azb
➜  /Users/Chihokyo
```

## 3.如何共享容器内部和宿主机数据？容器数据卷。

自己的一些理解吧。就是给Docker里面的容器做了备份，保存在了本地。这样就可以备份和持久化。

#### 命令

`docker run -it -v 宿主机的绝对路径:容器的绝对路径 镜像名`

`docker run -it -v /HostDataVolume:/ContainerDataVolume` 这里一定是镜像名

遇到了Mac上保存的问题，

```shell
➜  /Users/Chihokyo/Code/developer-skills/docker git:(master) ✗ docker run -it -v /HostDataVolume:/ContainerDataVolume centos
docker: Error response from daemon: Mounts denied:
The path /HostDataVolume
is not shared from OS X and is not known to Docker.
You can configure shared paths from Docker -> Preferences... -> File Sharing.
See https://docs.docker.com/docker-for-mac/osxfs/#namespaces for more info.
```

解决

这时候要打开设置，允许共享的数据卷必须要在这里设置的目录下。

![](https://raw.githubusercontent.com/chihokyo/image_host/master/20200829002340.png)

重新写。成功了，这样2个文件就可以联动了。

```shell
$ docker run -it -v /tmp/HostDataVolume:/ContainerDataVolume centos
[root@1c72498fdbb8 /]# ls -l
total 48
drwxr-xr-x   2 root root   64 Aug 28 15:24 ContainerDataVolume
lrwxrwxrwx   1 root root    7 May 11  2019 bin -> usr/bin
drwxr-xr-x   5 root root  360 Aug 28 15:24 dev
drwxr-xr-x   1 root root 4096 Aug 28 15:24 etc
drwxr-xr-x   2 root root 4096 May 11  2019 home
lrwxrwxrwx   1 root root    7 May 11  2019 lib -> usr/lib
lrwxrwxrwx   1 root root    9 May 11  2019 lib64 -> usr/lib64
drwx------   2 root root 4096 Aug  9 21:40 lost+found
drwxr-xr-x   2 root root 4096 May 11  2019 media
drwxr-xr-x   2 root root 4096 May 11  2019 mnt
drwxr-xr-x   2 root root 4096 May 11  2019 opt
dr-xr-xr-x 161 root root    0 Aug 28 15:24 proc
dr-xr-x---   2 root root 4096 Aug  9 21:40 root
drwxr-xr-x  11 root root 4096 Aug  9 21:40 run
lrwxrwxrwx   1 root root    8 May 11  2019 sbin -> usr/sbin
drwxr-xr-x   2 root root 4096 May 11  2019 srv
dr-xr-xr-x  13 root root    0 Aug 28 08:01 sys
drwxrwxrwt   7 root root 4096 Aug  9 21:40 tmp
drwxr-xr-x  12 root root 4096 Aug  9 21:40 usr
drwxr-xr-x  20 root root 4096 Aug  9 21:40 var
[root@1c72498fdbb8 /]#
```

查看是否挂载上了 `docker inspect CONTAINER ID`

```shell
➜  /private/tmp/HostDataVolume docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
1c72498fdbb8        centos              "/bin/bash"         5 minutes ago       Up 5 minutes                            condescending_payne
1a326f7527c4        centos              "/bin/bash"         7 hours ago         Up 12 minutes                           mycentos
➜  /private/tmp/HostDataVolume docker inspect 1c72498fdbb8
			"Mounts": [
            {
                "Type": "bind",
                "Source": "/tmp/HostDataVolume",
                "Destination": "/ContainerDataVolume",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            }
        ],
```

即使容器停止了，这俩文件夹也是同步的！

如果不想同步的话，请使用！

`docker run -it -v /HostDataVolume:/ContainerDataVolume:ro IMAGE`

```shell
$ docker run -it -v /tmp/HostDataVolume:/ContainerDataVolume:ro centos
[root@0c20523984a6 /]# ls -l
total 48
drwxr-xr-x   3 root root   96 Aug 28 15:27 ContainerDataVolume
lrwxrwxrwx   1 root root    7 May 11  2019 bin -> usr/bin
drwxr-xr-x   5 root root  360 Aug 28 15:37 dev
drwxr-xr-x   1 root root 4096 Aug 28 15:37 etc
drwxr-xr-x   2 root root 4096 May 11  2019 home
lrwxrwxrwx   1 root root    7 May 11  2019 lib -> usr/lib
lrwxrwxrwx   1 root root    9 May 11  2019 lib64 -> usr/lib64
drwx------   2 root root 4096 Aug  9 21:40 lost+found
drwxr-xr-x   2 root root 4096 May 11  2019 media
drwxr-xr-x   2 root root 4096 May 11  2019 mnt
drwxr-xr-x   2 root root 4096 May 11  2019 opt
dr-xr-xr-x 160 root root    0 Aug 28 15:37 proc
dr-xr-x---   2 root root 4096 Aug  9 21:40 root
drwxr-xr-x  11 root root 4096 Aug  9 21:40 run
lrwxrwxrwx   1 root root    8 May 11  2019 sbin -> usr/sbin
drwxr-xr-x   2 root root 4096 May 11  2019 srv
dr-xr-xr-x  13 root root    0 Aug 28 08:01 sys
drwxrwxrwt   7 root root 4096 Aug  9 21:40 tmp
drwxr-xr-x  12 root root 4096 Aug  9 21:40 usr
drwxr-xr-x  20 root root 4096 Aug  9 21:40 var
[root@0c20523984a6 /]# cd ContainerDataVolume/
[root@0c20523984a6 ContainerDataVolume]# touch contain.test
touch: cannot touch 'contain.test': Read-only file system
[root@0c20523984a6 ContainerDataVolume]#
```

这样只能在宿主机修改，不能在容器里修改文件，只读。

想要看一下是否设置成功还是用刚才的命令。

```shell
"Mounts": [
            {
                "Type": "bind",
                "Source": "/tmp/HostDataVolume",
                "Destination": "/ContainerDataVolume",
                "Mode": "ro",
                "RW": false, # 这里写入 是false
                "Propagation": "rprivate"
            }
],
```



## 4. Dockerfile

就是镜像的模板描述文件

Docker images 的描述文件 就是 Dockerfile 类似于shell的sh文件。

利用Dockerfile产生一个镜像,然后从镜像新建自己的容器。这个过程就是`Dockerfile`的意义。

其实这个file怎么写,网上都可以找到,但是最重要的就是运用。我把自己博客写的如何搭建Vue环境的`Dockerfile`现在拿过来。解析一下。

```dockerfile
FROM node:12.12.0-alpine   # 这里写容器(爸爸) 其实这里也是一个人写的版本 app:冒号后面是版本

WORKDIR /usr/src/app  # 进入到docker环境之后默认的路径

RUN apk update && \  # 运行的命令,先更新了所有
    npm install -g npm @vue/cli  

EXPOSE 4040  # 对外暴露的端口

CMD ["/bin/sh"]   # 进入之后执行的命令
```

接着用`Dockerfile`生成镜像。

```shell
$ docker build -t vue_app_image .  
```

build是命令

-t：为容器重新分配一个伪输入终端，通常与 -i 同

vue_app_image 镜像名字

. 当前文件夹

**这个时候应该就生成了新的镜像了,然后接下来就是开启容器**

```shell
docker run -v `pwd`:/usr/src/app -p 4040:4040 --name app -it -d vue_app_image
```

-v 数据容器卷 表示同步 pwd表示当前绝对路径,简单写法.

 -p 4040:4040 -p 指定端口 宿主机端口:容器内端口

--name 自定义命名(好找,不用每次复制粘贴ID了)

```shell
docker exec -it app sh
```

差不多就是这样了，不过官方这里也有一个写法。https://vuejs.org/v2/cookbook/dockerize-vuejs-app.html

这个写法主要面向的是已经开发好的Vuejs

```dockerfile
# 拉去node镜像
FROM node:lts-alpine

# 安装服务器
RUN npm install -g http-server

# 在当前目录下新建一个目录并自动定位
WORKDIR /app

# 拷贝node需要的模块
COPY package*.json ./

# 安装袭来
RUN npm install

# 拷贝项目文件或者目录到当前目录
COPY . .

# 运行编译
RUN npm run build

# 暴露端口
EXPOSE 8080

# 开启服务器
CMD [ "http-server", "dist" ]
```
从上面docker文件新建容器到当前目录

```shell
docker build -t vuejs-cookbook/dockerize-vuejs-app .
```

开始运行

```shell
docker run -it -p 8080:8080 --rm --name dockerize-vuejs-app-1 vuejs-cookbook/dockerize-vuejs-app
```

rm就是运行之后完删掉镜像