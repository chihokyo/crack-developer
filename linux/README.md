# Linux

## 工作上经常用到的一些命令

每一次总是记住一些没用的，所以就把工作上最长用的写一下了。

```bash
# 按照时间来排序，从最新的开始
ll -t
# 列出来XX开头的文件 比如 g开头的文件
ll g*
# 列出来XX结尾的文件 比如 后缀是md
ll *md

# 修改文件权限 r4w2e1 按照这个来记忆
chmod 777 文件名
chmod 755 文件名
# 有时候一时半会根本不知道怎么记忆上面的数字，我经常用用户+权限
-rw-r--r--  1 Chihokyo  staff    72K Dec  7 13:43 girl2.jpeg
#我一般都是用给所有用户 
chmod a+x 文件名 # 给所有用户增加x执行权限

# 查看最近的20个使用过的命令历史记录
history | tail -10
```

有时候会记不住sudo 和 su

**sudo** Super User Do 超级用户do

**su** Shift User 切换用户

工作上最常用的就是 `sudo su 用户` 利用root权限切换用户。无需输入切换用户密码了。

sudo su的含义就是要用root权限运行su命令，既然是用root权限运行su命令，那么就不需要输入切换到的用户的密码了。

话说偶尔也会遇到`su-`和`su`。区别就是 su - 不会保留当前环境。就是切换到其他目录的感觉了。

### 批量修改系列

```bash
# 把XXX全部修改成YYY
rename 's/\.csv/\.txt/' *
➜  /Users/Chihokyo/Code/javava git:(main) ✗ ll *g
-rw-r--r--@ 1 Chihokyo  staff    72K Sep  4 16:47 girl.jpeg
-rw-r--r--  1 Chihokyo  staff    72K Dec  7 13:43 girl2.jpeg
➜  /Users/Chihokyo/Code/javava git:(main) ✗ rename 's/\.jpeg/\.jpg/' *
➜  /Users/Chihokyo/Code/javava git:(main) ✗ ll *g
-rw-r--r--@ 1 Chihokyo  staff    72K Sep  4 16:47 girl.jpg
-rw-r--r--  1 Chihokyo  staff    72K Dec  7 13:43 girl2.jpg
➜  /Users/Chihokyo/Code/javava git:(main) ✗
```



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



