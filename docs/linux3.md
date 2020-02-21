# Linux Server Analysis



## 一、Mysql 数据库

除了使用容器运行数据库外，MySQL 还可以直接安装在主机上，直接在Linux 文件目录中留下痕迹。

数据库取证一般遵循以下步骤：

1、系统镜像可以仿真，并且能够直接使用MySQL 进行交互的，考虑去除root 用户密码，使用SQL语句检查整个数据库的内容

2、系统无法仿真的，或者仿真之后无法运行MySQL 的，考虑在不损坏或者已备份MySQL相关目录的前提下，修复运行环境，运行MySQL

3、系统无法仿真或者MySQL 文件目录不完整，单独排查MySQL 配置和数据目录中的文件



- 确定MySQL 数据文件的存放路径： 搜索ibdata1 文件：

  `find / -name ibdata1`

- 确定MySQL 版本：用16进制编辑器打开任意一个.frm 文件，检查0x34 和0x33 的位置，例如0x34 为0xC6，0x33 为0x29 ，则MySQL 版本为0xC629，即5.07.29

- 复制整个MySQL 数据目录，删除ib_logfile 文件，转移到新安装的MySQL 环境中，检索其中的数据

- 跳过root 密码：在 /etc/mysql/mysql.conf.d/mysqld.cnf 文件中[mysqld] 下添加 `skip-grant-tables`

- 重启MySQL 服务，使用root 登录



## 二、文件服务器（FTP，SMB，NAS）

### FTP

可以在CentOS 或Ubuntu 使用vsftpd 配置FTP 服务。FTP服务使用TCP 21端口验证身份信息，使用TCP 20端口传输数据。

- /etc/vsftpd/vsftpd.conf  vsftpd 的核心配置文件
- /etc/vsftpd/ftpusers    黑名单文件，此文件里的用户不允许访问 FTP 服务器
- /etc/vsftpd/user_list   白名单文件，此文件里的用户允许访问 FTP 服务器
- /etc/vsftpd.chroot_list 允许访问FTP 服务器的用户列表

** 配置文件也可能直接存放在/etc 目录下

vsftpd.conf 配置文件中的相关设置有：

| 配置变量       | 配置说明                 |
| :------------- | :----------------------- |
| local_root     | 登录FTP 服务器后所在目录 |
| xferlog_enable | Yes: 启用日志            |
| pasv_address   | 服务器所在IP地址         |

vsftp的日志文件保存在 /var/log/vsftpd.log，其中包含的信息有尝试登录此FTP 服务器的IP地址等



### Samba

Samba 是使用SMB 协议的一款软件。SMB 协议用来在Linux 与Windows 系统之间无痛共享存储与打印机

### NAS

NAS 通常用来将大容量磁盘挂载在网络上，形成网络存储。在电子取证领域，时常需要分析大容量磁盘，但其实NAS 本身的配置等内容不常出现。

NAS 本身不代表任何协议，它可以使用FTP，Samba，甚至HTTP 等协议传送文件。







参考：

1、http://www.xlysoft.net/detail/7-314-1194.html 效率源科技 如何对有访问密码的MySQL数据库进行取证

2、https://blog.csdn.net/hancoder/article/details/100988807 ubuntu下ftp的配置



