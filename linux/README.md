# Linux

## 个人觉得蛮好用的Linux网站

鸟哥私房菜 http://linux.vbird.org/linux_basic/

Linux 在线练习网站

顺便我觉得做题也是一个学习的很好的方式

## 虚拟机 -桥接模式

同样的网段可以进行联网，每一个网段的地址都是有限的 0-255。最多256个。

0不能用，255不能用。还有一个不能用。

这个模式的弊端就是有可能不够用。

好处：都在同一个网段，可以通信。

弊端：网段地址有限，可能造成IP冲突

## NAT模式

新产生一个IP地址。虚拟机不在占有网段。但是不在同一个网段不能互相通信。

虚拟机找到宿主机IP可以，但是宿主机无法直接找到虚拟机。 

通讯商

## 仅主机模式

单独的一台电脑

尝试添加一个用户

```bash
[root@48a0391ad249 home]#
[root@48a0391ad249 home]# pwd
/home
[root@48a0391ad249 home]# ls -la
total 8
drwxr-xr-x 1 root root 4096 Sep  2 07:09 .
drwxr-xr-x 1 root root 4096 Sep  2 06:51 ..
[root@48a0391ad249 home]# useradd demo_user
[root@48a0391ad249 home]# ls -la
total 12
drwxr-xr-x 1 root      root      4096 Sep  2 07:09 .
drwxr-xr-x 1 root      root      4096 Sep  2 06:51 ..
drwx------ 2 demo_user demo_user 4096 Sep  2 07:09 demo_user
[root@48a0391ad249 home]# userdel -r demo_user
[root@48a0391ad249 home]# ls -la
total 8
drwxr-xr-x 1 root root 4096 Sep  2 07:10 .
drwxr-xr-x 1 root root 4096 Sep  2 06:51 ..
[root@48a0391ad249 home]#
```

关于删除用户

```bash
# 删除用户
$ userdel 用户名
# 删除用户名和所有家目录资料
$ userdel -r 用户名
```

查看用户信息

```bash
$ id 用户名
[root@48a0391ad249 home]# id test_centos
uid=1001(test_centos) gid=1001(test_centos) groups=1001(test_centos)
```

切换用户

```bash
$ su - 用户名
```

关于组

组就是对一堆用户进行管理，比如批量赋予权力等等。

```bash
# 添加组
$ groupadd 组名
# 删除组
$ groupdel 组名
# 添加用户&指定组
useradd -g 用户组 用户名
# 直接修改组

```

增加完之后信息在哪里呢

查询磁盘占用情况 du -ach

顺便把shell基础也学了。

直接操作操作系统的语言，命令行解释器。

现在Python貌似抢了shell工作

### 执行权限问题



