# Linux Basic Analysis

文档侧重于以电子数据取证为目的的Linux 系统解析，主要包含Linux 文件系统目录、特殊的系统文件、常用软件的目录、文件与功能等，可能不会介绍Linux 开发的相关内容。



[TOC]




## 一、常见的Linux 发行版

DebianOS，以及其衍生版本：Ubuntu、LinuxMint、Deepin、Kali Linux

Redhat，以及其衍生版本：CentOS、Fedora

其他：Archlinux 等

 

**查看当前的Linux 发行版版本：**

lsb_release ：

`lsb_release -a`

uname ：

`uname -a`



查找\etc 中的文件：

对于Debian 及其衍生版：
`cat \etc\issue`
`cat \etc\issue.net`
`cat \etc\lsb-release`
`cat \etc\os-release`

对于CentOS：
`cat \etc\centos-release`
对于Fedora：
`cat \etc\fedora-release`

## 二、Linux 系统的典型目录结构（Dir Structure）

 

| 目录         | 说明                                                         |
| ------------ | ------------------------------------------------------------ |
| /            | 根目录，根目录下一般只有以下文件夹，没有文件\|               |
| /bin         | 存放了系统命令的可执行文件，例如cat、vi，相当于Windows 的系统环境变量PATH |
| /etc         | 存放了系统配置，例如release                                  |
| /home        | 用户文件夹，例如/home/user1 ，/home/user2                    |
| /usr         | 存放了用户应用的可执行文件、代码、动态链接库等，相当于Windows 的用户环境变量PATH |
| /usr/bin     | 用户安装的应用的可执行文件                                   |
| /usr/doc     | Linux 文档                                                   |
| /usr/include | 头文件目录                                                   |
| /usr/lib     | 存放动态链接库、软件包配置文件                               |
| /usr/man     | 执行man命令显示的帮助文档                                    |
| /usr/src     | 源代码，例如Linux内核源码: /usr/src/linux                    |
| /usr/local   | 本机单独增加的命令、动态链接库                               |
| /root        | root用户的用户文件夹被单独拿出来放到root目录里，不在usr目录里 |
| /opt         | 可选应用程序的安装目录                                       |
| /dev         | 存放设备文件（分字符设备和块设备）                           |
| /boot        | 系统引导                                                     |
| /lib         | 存放跟文件系统中的程序运行所需要的共享库及内核模块。共享库又叫动态链接共享库，作用类似windows里的.dll文件，存放了根文件系统程序运行所需的共享文件 |
| /tmp         | 临时文件夹                                                   |
| /var         | 日志                                                         |
| /lost+found  | 意外丢失的文件                                               |
| /proc        | 虚假的目录，磁盘中并不存在，其中的内容是系统映射出来的内存中的信息。 |





## 三、Linux 系统重要文件夹与文件的内容

1、 /etc/passwd

保存了用户数据库，其中包括用户名、加密过的密码等

2、/etc/group

保存了用户组的信息

3、/etc/issue

默认是发行版信息，实际上，文件里的内容用来显示在每次登录系统后终端显示的欢迎文字

4、/var/log

保存了许多程序的日志文件，例如登录日志

 

## 四、Linux 文件的属性（File Attribute）

 使用 `ls -l filename ` 命令查看文件的权限，例如：

-rw-r--r-- 1 root root 298 Sep 30 21:31 os-release

- 第一个字符 - 代表这是一个文件，d表示目录（即文件夹，在Linux内，文件夹也是一种特殊的文件），l表示链接

- 接下来三个字符rw- 表示文件的所有者对该文件可读（r，read）可写（w，write）但不可运行（x）

- 之后三个字符r-- 表示文件所有者所在的用户组 的其他用户们 对该文件可读，不可写，不可运行

- 最后三个r-- 表示其他用户对该文件可读，不可写，不可运行

- 数字1表示链接的文件数（与l类型的文件有关）

- 之后的root 表示为文件所属的用户

- 第二个root 表示为文件所属的用户（即root用户）所在的用户组为root用户组

- Sep 30 21:31 表示文件最后的更改日期

- os-release 为文件名

 

**如何修改文件或文件夹的权限： ** 

1、使用数字表示

 例如：rw-r--r--，以二进制表示为110,100,100，即644

`chmod 644 os-release`

例如 rwxrw-r--，以二进制表示为111,110,100，即764

`chmod 764 os-release`

2、使用字母表示

`chmod u+x os-release`

表示给文件所属的用户（u）增加（+）运行（x）权限；

`chmod g-wx os-release`

表示给文件所属用户的用户组（g）去除（-）写权限（w）和运行权限（x）

`chmod o+wx os-release`

表示给文件所属用户用户组之外的用户（o）增加（+）写权限（w）和运行权限（x）

`chmod a+r os-release`

表示给所有用户（a）增加读（r）权限

3、改变文件所有者

chown命令（change owner）

`chown user1 os-release `  将os-release文件所有者改为user1

chgrp命令（change group）

`chgrp basic—user os-release `  将os-release文件所属用户组改为basic-user



## 五、文件系统（File System）







参考：

1、 https://linux.cn/article-9586-1.html 查看Linux发行版名称与版本号的8种方法

2、 https://www.cnblogs.com/zhuchenglin/p/8686924.html Linux 各个目录的详细介绍

3、 https://www.cnblogs.com/123-/p/4189072.html Linux下用户组、文件权限详解