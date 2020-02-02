#MacOS Analysis

相对于用户数量庞大的Windows用户，MacOS的用户量相比之下要少很多。并且在近几年的取证赛题中对于MacOS的考察较少，针对性也比较弱，一般都依赖于对取证工具、Unix（Linux）系统的熟悉程度，总体来说难度较低。只要掌握工具的灵活运用与一些MacOS独有的小技巧，就能够轻松应试。

##MacOS系统简介

参考百度百科MacOS词条

[Mac OS](https://baike.baidu.com/item/Mac%20OS/2840867?fromtitle=MacOS&fromid=8654551&fr=aladdin)

##MacOS仿真与快速上手

苹果作为一家生态圈较为封闭的公司，MacOS绝大多数预装在苹果自家设备上，在PC机上安装黑苹果系统并不是一件容易的事情。因此推荐在虚拟机上安装MacOS并进行日常操作的熟悉。同时要对Linux文件目录结构有良好的认识，可以在资料查找过程中有更加明确的目的性。

现在大多数的仿真软件已经加入了对MacOS的支持，但是出于兼容性问题，并不是所有机器都能正常对赛题镜像完成仿真引导，如果出现引导失败或开机密码无法绕过的情况，就只能在取证大师中进行证据查找。

##MacOS取证相关工具

美亚取证大师

仿真工具

[osxcollector（开源的MacOS取证脚本，配合仿真进行使用）](https://github.com/Yelp/osxcollector)

##真题解析与小技巧

MacOS相关试题较少，因此以美亚杯2019年赛题举例。

1、考察镜像信息

![1](https://s2.ax1x.com/2020/02/02/1J76AS.png)

55、57、58三个题目的答案均可用取证大师轻松获取，难点就在于56题的硬件信息的获取。此时不可以用仿真+命令的方式读取配置，那样只能获取到当前虚拟机环境下分配的硬件信息。

MacOS下有一个名为HardwareConfig.json的文件记录了最近一次的计算机硬件信息，包含CPU、RAM等等，因此在取证大师中直接搜索该文件即可获取。

![1](https://s2.ax1x.com/2020/02/02/1tplsU.png)

2、考察证据文件搜集

![2](https://s2.ax1x.com/2020/02/02/1J7DnP.png)

取证大师并没有OCR搜索功能，且镜像中无关图片干扰会非常大，这时一般会考察到bash命令历史的考察。

![2](https://s2.ax1x.com/2020/02/02/1tpuR0.png)

在取证大师终端命令记录中可以看出一些对指定路径下图片文件的操作，其中modify文件十分可疑，定位到该文件夹下易得题目所找图片。

![2](https://s2.ax1x.com/2020/02/02/1tpQMT.png)

3、考察浏览痕迹

![3](https://s2.ax1x.com/2020/02/02/1J7s78.png)

这一类题目均为送分题，直接取证大师一把梭就可以轻松获取答案。

![3](https://s2.ax1x.com/2020/02/02/1tpnGq.png)

![3](https://s2.ax1x.com/2020/02/02/1tpKzV.png)

##相关网站信息获取

[远景Mac论坛](http://mac.pcbeta.com/)

[远景Hackintosh](http://bbs.pcbeta.com/forum-498-1.html)