# 题目示例

## 2019 美亚杯 部分题目

![image-20200308221738244](..\res\linux\image-20200308221738244.png)

使用PowerShell 计算文件sha1、sha256 或md5

```
Get-FileHash -Path FilePath -Algorithm sha1
Get-FileHash -Path FilePath -Algorithm sha256
Get-FileHash -Path FilePath -Algorithm md5
```

------

![image-20200308222137205](..\res\linux\image-20200308222137205.png)

NTFS ：Windows 使用的文件系统

EXT4：Linux 根目录挂载磁盘分区的文件系统

SWAP：Linux 交换空间使用的特殊文件系统

XFS：日志文件系统，某些Linux 可能会使用

------

![image-20200308222352308](..\res\linux\image-20200308222352308.png)

检查 ~/.bash_history 文件

git：代码仓库管理。```git clone url``` 指从url 位置克隆仓库到本地

slowhttptest：猜测为slowhttp攻击

vim：一种文本编辑器

------

![image-20200308222609332](..\res\linux\image-20200308222609332.png)

检查文件：```/var/lib/docker/containers/镜像ID/config.v2.json``` 找到Created 字段

或者查找 ```/var/lib/docker/image/overlay2/layerdb/mounts``` 对应文件夹的创建时间

------

![image-20200308223200080](..\res\linux\image-20200308223200080.png)



------

![image-20200308225235092](..\res\linux\image-20200308225235092.png)

检查文件 /etc/passwd

------

![image-20200308225333477](..\res\linux\image-20200308225333477.png)

boot.log：记录系统启动，可以排查启动时异常

wtmp：使用命令```last -f /var/log/wtmp```来查看登录历史信息

syslog：依次记录所有内容，除身份验证相关消息【我没见过这种文件】

-----

![image-20200308230247549](..\res\linux\image-20200308230247549.png)

使用取证大师查看Firefox 浏览记录

-----

![image-20200308230314495](..\res\linux\image-20200308230314495.png)

![image-20200308230357359](..\res\linux\image-20200308230357359.png)

检查 ~/.bash_history 文件

-----

## 2018 美亚杯 部分题目

//TODO: 拿到镜像之后再分析