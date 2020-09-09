# nginx

官方GitHub https://github.com/nginx/nginx

## 1. 概述

**静态资源就是。** 图片，样式等等，不会根据用户不同而改变。

**后端资源就是。**价格，数据这些需要跟其他应用配合进行实时计算。

*nginx*只能处理静态web服务器的请求。既然*nginx*本身无法处理动态资源请求，那怎么处理客户端发来的请求呢

那就是**反向代理**，从客户端发出来的请求，反向代理给各种各样的服务器，让他们处理请求。

比如https请求给web服务器进行处理。邮件给mail server进行处理。



![](https://raw.githubusercontent.com/chihokyo/image_host/master/20200909144045.png)



动态请求要看后面的app应用程序，*nginx*本身并不处理请求，但他交给别人处理。但如何请求变多了怎么办？

**负载均衡**就出来了，*nginx*可以对多台服务器进行控制处理请求。

apache请求低效性，单核处理。不适合并发。而*nginx*是非阻塞式的请求。

## 2. 应用场景

应用程序服务器。数据库服务器。

![](https://raw.githubusercontent.com/chihokyo/image_host/master/20200909144907.png)

在这些请求里面，**静态资源（Web Server）**可能响应的最快，其次是**应用程序**（就是通常写的PHP，JAVA这些服务器语言），然后可能**数据库还有查询写**入等等请求。这三者来说，速度肯定是不一样的，就造成了请求的时候可能会有偏差，这个时候可以在数据库这里设置一个缓存（有些有有些没有。）

**<u>其实缓存就是一个中间件，用来弥补性能上的差异。</u>**

应用程序可能增加到了3台，这时候*nginx*就需要给多台服务器应用程序服务。那么**负载均衡**就出现了。主要是给多个服务器同时提供反向代理服务。

而**缓存加速**是什么呢，就是一旦你缓存过的内容，*nginx*这边会给你记录下来，这样就会更快速的进行显示。

API服务是之后开发提供的能力。用来提供一部分应用服务。

![](https://raw.githubusercontent.com/chihokyo/image_host/master/20200909144940.png)



#### 核心优势

- 高并发，高性能。（用的内存少，处理请求多。）

- 扩展性好。（模块化设计，解耦。还允许第三方进行开发。）
- 异步非阻塞的时间驱动模型。（不会阻塞，效率高，阻塞的时候可以处理其他请求）
- 热更新
- BSD许可（二次开发）

## 3. 一些说明配置

etc 配置文件

usr 程序文件

```shell
[root@64797ab4d690 /]# rpm -ql nginx
/etc/logrotate.d/nginx
/etc/nginx/fastcgi.conf
/etc/nginx/fastcgi.conf.default
/etc/nginx/fastcgi_params
/etc/nginx/fastcgi_params.default
/etc/nginx/koi-utf 
/etc/nginx/koi-win
/etc/nginx/mime.types
/etc/nginx/mime.types.default
/etc/nginx/nginx.conf # 主配置文件
/etc/nginx/nginx.conf.default
/etc/nginx/scgi_params
/etc/nginx/scgi_params.default
/etc/nginx/uwsgi_params
/etc/nginx/uwsgi_params.default
/etc/nginx/win-utf
/usr/bin/nginx-upgrade
/usr/lib/.build-id
/usr/lib/.build-id/2d
/usr/lib/.build-id/2d/da6018ae12edb856ad3d2cf61bf586b6b4873c
/usr/lib/systemd/system/nginx.service
/usr/lib64/nginx/modules
/usr/sbin/nginx
/usr/share/doc/nginx # 帮助文件
/usr/share/doc/nginx/CHANGES
/usr/share/doc/nginx/README
/usr/share/doc/nginx/README.dynamic
/usr/share/licenses/nginx
/usr/share/licenses/nginx/LICENSE
/usr/share/man/man3/nginx.3pm.gz
/usr/share/man/man8/nginx-upgrade.8.gz
/usr/share/man/man8/nginx.8.gz
/usr/share/nginx/html/404.html
/usr/share/nginx/html/50x.html
/usr/share/nginx/html/index.html
/usr/share/nginx/html/nginx-logo.png
/usr/share/nginx/html/poweredby.png
/usr/share/vim/vimfiles/ftdetect/nginx.vim
/usr/share/vim/vimfiles/indent/nginx.vim
/usr/share/vim/vimfiles/syntax/nginx.vim
/var/lib/nginx # nginx 目前还没有文件，起来之后创建，一个是访问记录文件，一个是错误日志。
/var/lib/nginx/tmp
/var/log/nginx
[root@64797ab4d690 /]#
```

有一些配置都在 `/etc/nginx/nginx.conf`

下面是找到了程序的二进制文件。这里可以进行查看

```shell
[root@64797ab4d690 nginx]# rpm -ql nginx | grep bin
/usr/bin/nginx-upgrade
/usr/sbin/nginx
[root@64797ab4d690 nginx]# /usr/sbin/nginx -h
nginx version: nginx/1.14.1
Usage: nginx [-?hvVtTq] [-s signal] [-c filename] [-p prefix] [-g directives]
# 这些中括号都是可选的

Options:
  -?,-h         : this help
  -v            : show version and exit
  -V            : show version and configure options then exit
  -t            : test configuration and exit
  -T            : test configuration, dump it and exit
  -q            : suppress non-error messages during configuration testing
  -s signal     : send signal to a master process: stop, quit, reopen, reload
  -p prefix     : set prefix path (default: /usr/share/nginx/)
  -c filename   : set configuration file (default: /etc/nginx/nginx.conf)
  -g directives : set global directives out of configuration file

[root@64797ab4d690 nginx]#
```

开启服务之后，这个时候这里就有了日志

二进制开启服务`/usr/sbin/nginx`

```shell
[root@64797ab4d690 nginx]# cd /var/log/nginx/
[root@64797ab4d690 nginx]# ls -l
total 0
-rw-r--r-- 1 root root 0 Sep  9 06:24 access.log
-rw-r--r-- 1 root root 0 Sep  9 06:24 error.log
[root@64797ab4d690 nginx]#
```

## 4. 进程结构图

边缘节点上，第一个请求就到了nginx，多线程最大的问题就是，一个线程错误，其他都出问题。

所以采用了**多线程**

![](https://raw.githubusercontent.com/chihokyo/image_host/master/20200909163751.png)

### Linux信号量

kill掉pid，其实就是发送了一个信号。kill -9 就是一个强制。

```shell
[root@64797ab4d690 nginx]# kill -l
 1) SIGHUP	 2) SIGINT	 3) SIGQUIT	 4) SIGILL	 5) SIGTRAP
 6) SIGABRT	 7) SIGBUS	 8) SIGFPE	 9) SIGKILL	10) SIGUSR1
11) SIGSEGV	12) SIGUSR2	13) SIGPIPE	14) SIGALRM	15) SIGTERM
16) SIGSTKFLT	17) SIGCHLD	18) SIGCONT	19) SIGSTOP	20) SIGTSTP
21) SIGTTIN	22) SIGTTOU	23) SIGURG	24) SIGXCPU	25) SIGXFSZ
26) SIGVTALRM	27) SIGPROF	28) SIGWINCH	29) SIGIO	30) SIGPWR
31) SIGSYS	34) SIGRTMIN	35) SIGRTMIN+1	36) SIGRTMIN+2	37) SIGRTMIN+3
38) SIGRTMIN+4	39) SIGRTMIN+5	40) SIGRTMIN+6	41) SIGRTMIN+7	42) SIGRTMIN+8
43) SIGRTMIN+9	44) SIGRTMIN+10	45) SIGRTMIN+11	46) SIGRTMIN+12	47) SIGRTMIN+13
48) SIGRTMIN+14	49) SIGRTMIN+15	50) SIGRTMAX-14	51) SIGRTMAX-13	52) SIGRTMAX-12
53) SIGRTMAX-11	54) SIGRTMAX-10	55) SIGRTMAX-9	56) SIGRTMAX-8	57) SIGRTMAX-7
58) SIGRTMAX-6	59) SIGRTMAX-5	60) SIGRTMAX-4	61) SIGRTMAX-3	62) SIGRTMAX-2
```

子进程发送信号给父进程，比如如果自己done掉了就发给了父进程一个信号。

![](https://raw.githubusercontent.com/chihokyo/image_host/master/20200909164509.png)

master 命令行管理，也是通过发送信号量来管理的。

```shell
[root@64797ab4d690 nginx]# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 06:11 pts/0    00:00:00 /bin/bash
root       135     1  0 06:24 ?        00:00:00 nginx: master process /usr/sbin/nginx # 父进程
nginx      136   135  0 06:24 ?        00:00:00 nginx: worker process # 子进程
nginx      137   135  0 06:24 ?        00:00:00 nginx: worker process # 子进程
root       145     1  0 07:47 pts/0    00:00:00 ps -ef
```

一般有几个cpu，有几个work子进程。

这个数量可以在配置文件里面进行修改。

```shell
[root@64797ab4d690 nginx]# less /etc/nginx/nginx.conf
user nginx;
worker_processes auto; # 这里可以修改
error_log /var/log/nginx/error.log;
```

## 5. 配置文件重载原理真相

平滑升级，热更新。

![](https://raw.githubusercontent.com/chihokyo/image_host/master/20200909170350.png)

## 6. 热部署

可以在有业务的情况下。然后进行升级。

1 讲旧的nginx文件替换成新的nginx文件（如果目录已经改变，不适用）

2 给master进程发送USR2信号。

3 master进程修改pid文件，加后缀.oldbin

4 master进程会用新的文件启动新的master 这时候会并存

5 向旧的master进程发送WINCH信号，旧的worker子进程退出

6 回滚情形。向旧master发送HUP，向新的master发送QUIT

![](https://raw.githubusercontent.com/chihokyo/image_host/master/20200909170859.png)

