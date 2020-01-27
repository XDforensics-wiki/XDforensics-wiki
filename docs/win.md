# Windows Analysis

与挖掘Linux服务器镜像中的网络行为信息不同，取证比赛中对于Windows镜像的考查主要体现在使用痕迹与文件上，比如软件使用记录，浏览器历史记录，病毒样本文件，以及爬取的表单话单等。

因此取证工作者要熟练地掌握文件提取、数据恢复、系统仿真、详单分析、编程与逆向工程等知识与技术手段。

## 磁盘结构

从物理层面，一个磁盘按层次分为 ：
磁盘组合 → 单个磁盘 → 某一盘面 （platter）→ 某一磁道 （track）→ 某一扇区（sector）

关于簇和扇区：
文件占用磁盘空间时，基本单位不是字节而是簇。簇的大小与磁盘的规格有关，一般情况下，软盘每簇是1个扇区，硬盘每簇的扇区数与硬盘的总容量大小有关，可能是4、8、16、32、64……

Windows文件系统：
通常在Windows平台下使用的3种文件系统是FAT（文件分区表），FAT32（32位文件分区表）和NTFS（NT文件系统）。
在FAT文件系统下，每一个磁盘被分成固定大小的簇。簇最少为512个字节，其大小可以成倍增长，最大为32K。每个簇都是由唯一的索引号——一个16位二进制数来标识。因为16位二进制数最大为65536，所以FAT分区所拥有的簇的数量不可能超过65536个。
FAT32文件的原理几乎与此相同，但它的簇更小，而且由于FAT32入口是32位，所以其容量理论上可以超过40亿个字节。
NTFS是一个相当高级的文件系统。它的主文件表（MFT）是一个非常完整的数据库，它负责对磁盘上的每个文件进行索引。每个MFT的入口通常为1K大小，其中记录了大量的文件信息。NTFS可以在文件的MFT入口中存储非常小的文件的全部内容；对于大一些的文件，这些入口会标识出包含文件数据的簇。

## 信息提取与分析

推荐使用的有两种软件：美亚的取证大师和弘连的证据分析软件。

二者各有各的优缺点，取证大师主要体现在广度，功能非常齐全。在自动取证结果中能够直接提供很多有用的信息（如时间线，软件使用痕迹等），省去很多麻烦。19年增加小程序板块后，功能的扩展性也极好，能够调用python等脚本去做日志分析等，还提供了包括如正则表达式在内的匹配方法，筛选功能强大。

![avatar](https://k1ng0fic3.github.io/images/wiki3.png)

火眼证据分析软件补充了取证大师在处理数据库和备份文件上的缺陷。不仅能够对SQL数据库进行直接预览和处理，而且能将PC中的iOS与Android备份文件作为证据文件，再次进行取证分析，甚至将手机中的微信记录等细节直观的投到屏幕，在遇到PC+手机备份的情况下较为实用。

![avatar](https://k1ng0fic3.github.io/images/wiki4.png)

## 系统仿真

系统仿真是指将镜像文件或者对象计算机系统的硬盘模拟为虚拟机，在虚拟机环境下进行启动，我们就可以以交互的方式和系统用户的角度直观的检查和操作目标系统，查看相关信息。

目前推荐使用的软件工具是弘连的火眼仿真软件，能方便地实现锁定系统版本和绕过开机密码，并调用本机的VMware虚拟机实现仿真，如下：

![avatar](https://k1ng0fic3.github.io/images/wiki1.png)

![avatar](https://k1ng0fic3.github.io/images/wiki2.png)

## 知识整理（持续完善中）

#### 磁盘容量

首先需要理解[4K对齐](https://baike.baidu.com/item/4K%E5%AF%B9%E9%BD%90/3778627?fr=aladdin)的原理。

电脑传统机械硬盘的每个扇区一般大小为512个字节，然而随着时代发展，硬盘容量不断扩展，于是将每个扇区512字节改为每个扇区4096个字节，也就是现在常说的“4K扇区”。
随着NTFS成为了标准的硬盘文件系统，其文件系统的默认分配单元大小（簇）也是4096字节，为了使簇与扇区相对应，即使物理硬盘分区与计算机使用的逻辑分区对齐，保证硬盘读写效率，所以就有了“4K对齐”的概念。

例（2019美亚杯个人4）

![avatar](https://k1ng0fic3.github.io/images/wiki5.png)

通过取证大师查看：

![avatar](https://k1ng0fic3.github.io/images/wiki6.png)

得知D盘为系统（OS）盘，扇区数为```961099776```

![avatar](https://k1ng0fic3.github.io/images/wiki7.png)

再次确认逻辑扇区的大小为```512```字节，故需要空出4096/512=```8```个扇区的空间，即:
(961099776 - 8) * 512  = ```492,083,081,216```

#### 时间戳

由于在数据库等信息储存介质中，经常使用[时间戳(timestamp)](https://baike.baidu.com/item/%E6%97%B6%E9%97%B4%E6%88%B3)来记录时间信息，需要理解并能够把它与标准时间进行互相转换。

例（2019美亚杯个人41）

![avatar](https://k1ng0fic3.github.io/images/wiki8.png)

利用取证大师得到上传的标准时间（年-月-日 时:分:秒）2019-10-31 15:22:31

![avatar](https://k1ng0fic3.github.io/images/wiki11.png)

最后使用Python库函数转换为时间戳，附代码：

![avatar](https://k1ng0fic3.github.io/images/wiki9.png)

>'''标准时间到时间戳'''
import time
a = time_str
timeArray = time.strptime(a,"%Y-%m-%d %H:%M:%S") 
timeStamp = int(time.mktime(timeArray))
'''时间戳到标准时间'''
import time
timeArray = time.localtime(timeStamp)
otherStyleTime = time.strftime("%Y-%m-%d %H:%M:%S", timeArray)

测试：

![avatar](https://k1ng0fic3.github.io/images/wiki10.png)

#### 动态链接库

例（2019美亚杯个人15）

![avatar](https://k1ng0fic3.github.io/images/wiki12.png)

想在镜像文件中找到动态链接库的调用情况可能比较困难，不妨换个思路。

利用取证大师提取出该exe程序后在本地运行，再利用火绒剑查看即可。

![avatar](https://k1ng0fic3.github.io/images/wiki13.png)