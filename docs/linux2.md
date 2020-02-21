# Linux Program Analysis

## 一、Linux 下的Docker 容器

我们可以把Docker 当作多功能沙盒，可以将需要的软件放在一个容器内运行，而不干扰容器外的系统。例如，在两个个容器内分别运行nginx服务器，容器一对外开放端口10801，容器二对外开放端口10802，这样，在同一台主机内，两个web服务器就可以同时互不干扰地运行。

### 使用Docker 时，主要区分以下两个概念：

1、Docker镜像 image

2、容器 Container

我们可以在Docker Hub上下载各种images，例如nginx，mysql，ubuntu等，也可以将自己的软件运行环境打包成一个image

使用image来新建一个container

### Docker 入门

例如：

查阅Docker Hub内的[官方MySQL 文档](https://hub.docker.com/_/mysql)，首先下载MySQL镜像

`docker pull mysql`

如果要指定某个版本的MySQL，在mysql后加冒号指定版本，例如：

`docker pull mysql:8.0.10`

不指定版本则默认为latest（最新版）

等待下载完成，下载完成后查看目前已经下载好的images：

`docker images`

确认镜像已经下载成功，接下来使用这个镜像新建一个容器：

`docker run --name first_mysql_container -p 23306:3306 -e MYSQL_ROOT_PASSWORD=yourpassword -d mysql`

其中的命令和参数分别为：

- run：docker操作，用来新建容器；

- --name first_mysql_container：给容器命名，可选；

- -p 23306:3306：端口映射，将容器内的3306端口映射到主机的23306端口

- -e MYSQL_ROOT_PASSWORD=yourpassword：设置MySQL root密码

- -d ：容器创建后将会在后台运行，命令执行后返回一个container id

- mysql：使用mysql镜像，如果要指定版本，则写为 mysql:tag

使用命令 `docker ps `来查看已经创建的容器，Docker会显示以下详情：

- CONTAINER ID ：唯一的容器id，对应每一个单独的容器
- IMAGE：这个容器是使用什么镜像创建的
- COMMAND
- CREATED： 容器创建的时间
- STATUS：容器已经运行了或者停止了多长时间
- PORTS：端口映射情况
- NAMES：容器的名字

创建好容器之后，进入容器，亲手操作mysql：

`docker exec -it first_mysql_container bash`

- exec：docker命令，在运行的容器中执行命令
- -it：我一般都加上这两个参数
- first_mysql_container：容器名字，此处填容器ID亦可
- bash：在这个容器内运行bash

 之后输入 `mysql -u root -p` 即可使用MySQL



### Docker 的配置目录与文件分析

1、如果系统还可以仿真运行，docker服务可以启动，使用一下命令：

`docker version` 查询docker client 和server 版本，

`docker info` 可以详细调查正在运行、暂停或停止的container数量，是否使用了docker集群需要的swarm网络模式，docker镜像源地址等

`docker sespect ImageID` 查询某个镜像的详细信息



2、如果系统不能仿真，或是仿真之后无法启动docker服务，应该首先考虑修复系统丢失的文件或进行合理配置，恢复docker服务。

3、如果只能从硬盘分析入手，则考虑一下方法：

- 要确定Docker Server的版本，可以查找apt 包管理程序的日志：/var/log/apt/term.log

- 查看已经下载了哪些镜像，检查 /var/lib/docker/image/overlay2/repositories.json 文件

\* 需要注意的是，docker image 使用OverlayFS 分层存储在/var/lib/docker/overlay2，不好区分

- 查看container的详细信息，在/var/lib/docker/containers找到对应containerID 的文件夹，查看config.v2.json 中的内容，其中包含：当前容器是否在运行、暂停或者停止，容器运行的虚拟主机名称Hostname，在容器内使用的端口ExposedPorts，容器内的环境变量Env，容器所使用的镜像ID，容器映射的端口Network Settings.Ports 等等



### Docker 网络



//TODO: 等待完成



## 二、运行在Linux 上的Mysql 数据库

除了使用容器运行数据库外，MySQL 还可以直接安装在主机上，直接在Linux 文件目录中留下痕迹。

数据库取证一般遵循以下步骤：

1、系统镜像可以仿真，并且能够直接使用MySQL 进行交互的，考虑去除root 用户密码，使用SQL语句检查整个数据库的内容

2、系统无法仿真的，或者仿真之后无法运行MySQL 的，考虑在不损坏或者已备份MySQL相关目录的前提下，修复运行环境，运行MySQL

3、系统无法仿真或者MySQL 文件目录不完整，单独排查MySQL 配置和数据目录中的文件



- 确定MySQL 数据文件的存放路径： 搜索ibdata1 文件：

  `find / -name ibdata1`

- 确定MySQL 版本：用16进制编辑器打开任意一个.frm 文件，检查0x34 和0x33 的位置，例如0x34 为0xC6，0x33 为0x29 ，则MySQL 版本为0xC629，及5.07.29

- 复制整个MySQL 数据目录，删除ib_logfile 文件，转移到新安装的MySQL 环境中，检索其中的数据

- 跳过root 密码：在 /etc/mysql/mysql.conf.d/mysqld.cnf 文件中[mysqld] 下添加 `skip-grant-tables`

- 重启MySQL 服务，使用root 登录





参考：

1、https://www.jianshu.com/p/806485990aea Docker文件目录 - 简书

2、https://hub.docker.com/_/mysql mysql - Docker Hub

3、李鹏超, 周凯. 基于Docker容器的电子数据取证方法[J]. 吉林大学学报(理学版), 2019, 57(06): 1485-1490.

4、http://www.xlysoft.net/detail/7-314-1194.html 效率源科技 如何对有访问密码的MySQL数据库进行取证