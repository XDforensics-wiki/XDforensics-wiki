---
title: 内存取证原理学习及Volatility - 篇一
date: 2019-12-08 10:18:01
tags: 电子取证
---

> 网络攻击内存化和网络犯罪隐遁化,使部分关键数字证据只存在于物理内存或暂存于页面交换文件中,这使得传统的基于文件系统的计算机取证不能有效应对。

<!--More-->

## 内存取证定义及其特点
　　先给出官方的指导指南`《The Art Of Memeory Forensics》`一书中给出的介绍(后面会给出下载地址)，个人认为非常清晰：
```txt
  Memory forensics is arguably the most fruitful, interesting, and provocative realm
of digital forensics. Each function performed by an operating system or applica-
tion results in specific modifications to the computer’s memory (RAM), which can often
persist a long time after the action, essentially preserving them. Additionally, memory
forensics provides unprecedented visibility into the runtime state of the system, such as
which processes were running, open network connections, and recently executed com-
mands. You can extract these artifacts in a manner that is completely independent of the
system you are investigating, reducing the chance that malware or rootkits can interfere
with your results. Critical data often exists exclusively in memory, such as disk encryp-
tion keys, memory-resident injected code fragments, off-the-record chat messages, unen-
crypted e-mail messages, and non-cacheable Internet history records.
```
　　从你按下计算机电源的那一刻起，内存就开始工作了，当然其他硬件也在工作，但是内存会加载很多的重要数据，如操作系统的加载、预启动程序的加载、正常运行时程序程序的加载等等。
　　**内存取证的一个特点就是它提供了一种清晰的途径来让人们对计算机实时的状态进行取证，如当时的进程情况、网络连接情况、执行过的命令等等**，这对提取一些木马程序等恶意程序是有很大帮助的，除此之外，你甚至可以从内存中取出一些密码信息，如mimikatz就是这么干的。
　　
## Volatility简介
　　Volatility是一个用于内存取证的框架工具，集成了多种模块，支持市面常见的操作系统。
　　下面简单浏览一下大概界面和常用模块：
　　![界面](https://float311.gitee.io/img/内存取证原理学习及Volatility - 篇一/界面.png)
　　这是volatility的主页面，也有GUI的exe版本，但是没有Python版的扩展性强(如集成mimikatz)
　　下面列出常用的模块：
```txt
imageinfo：显示目标镜像的摘要信息，这常常是第一步---获取内存的操作系统类型及版本，之后可以在 –profile 中带上对应的操作系统，后续操作都要带上这一参数
pslist：该插件列举出系统进程，但它不能检测到隐藏或者解链的进程，psscan可以
psscan：可以找到先前已终止(不活动)的进程以及被rootkit隐藏或解链的进程
pstree：以树的形式查看进程列表，和pslist一样，也无法检测隐藏或解链的进程
mendump：提取出指定进程，常用foremost 来分离里面的文件(历年美亚杯有此题)
filescan：扫描所有的文件列表
hashdump：查看当前操作系统中的 password hash，例如 Windows 的 SAM 文件内容(实际中没有mimikatz效果好)
svcscan：扫描 Windows 的服务
connscan：查看网络连接
cmdscan：可用于查看终端记录
dlllist: 列出某一进程加载的所有dll文件
dumpfiles：导出某一文件(指定虚拟地址)
hivelist: 列出所有的注册表项及其虚拟地址和物理地址
timeliner: 将所有操作系统事件以时间线的方式展开
```
## 2019美亚杯内存取证部例题
　　这里我选了几个简单的和几个示例性的例题，作为参考，题目来源为2019年的美亚杯个人赛。
　　
　　1. 例题1
　　![例题1](https://float311.gitee.io/img/内存取证原理学习及Volatility - 篇一/例题1.png)
　　这道题出了基本就是第一题，因为操作系统及硬件架构这一信息是必要的。使用`imageinfo`模块就可以了，之后volatility会给出几个结果，其中一般第一个就是对的，不对还可以继续尝试其他可能，正确的标志就是你使用这个`profile`可以调用模块了，错误的`profile`是不能正常使用模块的。结果：
　　![例题1解](https://float311.gitee.io/img/内存取证原理学习及Volatility - 篇一/例题1解.png)
　　
 　　2. 例题2
　　![例题2](https://float311.gitee.io/img/内存取证原理学习及Volatility - 篇一/例题2.png)
　　查询进程信息使用`pslist`模块，这里会列出系统进程的PID、PPID、时间等等信息。结果：
　　![例题2解](https://float311.gitee.io/img/内存取证原理学习及Volatility - 篇一/例题2解.png)
　　
　　3. 例题3
　　![例题3](https://float311.gitee.io/img/内存取证原理学习及Volatility - 篇一/例题3.png)
　　查看当前的网络连接情况可以使用`netscan`模块，该模块会列出所有实时的网络连接信息，包括使用的协议类型、源地址、目的地址、双方的端口号、连接状态(LISTENING、CLOSED等)。结果：
　　![例题3解](https://float311.gitee.io/img/内存取证原理学习及Volatility - 篇一/例题3解.png)

　　4. 例题4
　　![例题4](https://float311.gitee.io/img/内存取证原理学习及Volatility - 篇一/例题4.png)
　　内存取证中的一个重要步骤就是解析注册表，Windos注册表会帮助你获得很多隐藏的信息以及一些重要的信息(如软件信息、系统信息、硬件信息等等)，如这个题中的`SAM`文件中就储存着加密的账户密码信息，这个注册表项有助于我们获取密码(如hashdump模块)。结果：
　　![例题4解](https://float311.gitee.io/img/内存取证原理学习及Volatility - 篇一/例题4解.png)
　　
　　5. 例题5
　　![例题5](https://float311.gitee.io/img/内存取证原理学习及Volatility - 篇一/例题5.png)
　　因为在例题三中我们看到他访问了同一网段上的一台机器上的SMB服务(445端口)，所以猜测盘符E实际上位于那台机器上，并且考虑到牵扯时间的问题，所以使用`timeliner`模块，该模块会以时间线的方式，列出计算机所有活动。
　　可以看到2019-10-31 06:59:45时，使用了explorer.exe文件管理器，以TMP_User的身份访问了E盘上 的Personal Information.xlsx。
　　![例题5解](https://float311.gitee.io/img/内存取证原理学习及Volatility - 篇一/例题5解.png)
　　
　　6. 例题6
　　![例题6](https://float311.gitee.io/img/内存取证原理学习及Volatility - 篇一/例题6.png)
　　**本题是美亚杯第一次在内存取证部分涉及硬件信息的搜寻，以后应多加训练**，实际上本题是对注册表的解析，因为USB设备的信息实际上会在首次使用时被记录至注册表中，下面时分析过程：
　　先使用`svcscan`模块进行服务的扫描：
　　![例题6解-1](https://float311.gitee.io/img/内存取证原理学习及Volatility - 篇一/例题6解-1.png)
　　可以看到USB的相关服务，可以确定有USB设备。
　　
　　使用`hivelist`模块结合`printKey`模块进行注册表项的查找，在下列表项中可以看到USB设备的详细信息为希捷的设备：
　　![例题6解-2](https://float311.gitee.io/img/内存取证原理学习及Volatility - 篇一/例题6解-2.png)

　　最终可以在注册表中看大该设备的GUID，最终排除改该选项得出答案为E选项:
　　![例题6解-3](https://float311.gitee.io/img/内存取证原理学习及Volatility - 篇一/例题6解-3.png)
　　
## 结束语
　　本篇只是对内存取证做简单介绍，以及2019美亚杯内存取证部分例题的一个简单浏览，后续会更新详细的关于内存取证方面的博客。
　　
## 参考学习链接
[浅谈内存取证](https://www.freebuf.com/column/186799.html)
[《The Art Of Memeory Forensics》](https://bookist.cc/book/37522120704)
