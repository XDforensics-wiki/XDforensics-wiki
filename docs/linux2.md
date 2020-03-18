# Linux Program Analysis



## 一、Linux 下的Docker 容器

我们可以把Docker 当作多功能沙盒，可以将需要的软件放在一个容器内运行，而不干扰容器外的系统。例如，在两个个容器内分别运行nginx服务器，容器一对外开放端口10801，容器二对外开放端口10802，这样，在同一台主机内，两个web服务器就可以同时互不干扰地运行。

### 使用Docker 时，主要区分以下两个概念：

1、Docker镜像 image

2、容器 Container

我们可以在Docker Hub上下载各种images，例如nginx，mysql，ubuntu等，也可以将自己的软件运行环境打包成一个image

使用image来新建一个container

举个栗子：用一个Windows 7 iso镜像，可以给好多电脑安装系统。用一个MySQL Docker 镜像，可以创建好多个MySQL 容器

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



## 二、OpenSSH

使用SSH 来远程连接Linux 主机。

被连接的主机：安装SSH server

进行连接的主机：安装SSH client

查看SSH 连接日志、SSH config 文件可以检索到曾经连接过这台主机的客户机IP地址。

常见的几个文件：

| 文件名                 | 说明                                                       |
| ---------------------- | :--------------------------------------------------------- |
| /var/log/secure        | 日志文件，记录登录历史记录                                 |
| ~/.ssh/config          | ssh 配置文件，用来进行快速密钥认证登录，其中可能记录了私钥 |
| ~/.ssh/authorized_keys | 记录公钥                                                   |

密钥认证基本流程：

使用ssh-keygen 命令生成一对密钥，私钥放在主机A 的config 文件里（当然需要编辑host等字段），公钥放在主机B 的authorized_keys 内，主机A 则可以无需密码通过SSH 连接主机B。







参考：

1、https://www.jianshu.com/p/806485990aea Docker文件目录 - 简书

2、https://hub.docker.com/_/mysql mysql - Docker Hub

3、李鹏超, 周凯. 基于Docker容器的电子数据取证方法[J]. 吉林大学学报(理学版), 2019, 57(06): 1485-1490.

4、

