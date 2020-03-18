# Packet Analysis

网络流量分析是指捕捉网络中流动的数据包，并通过查看包内部数据以及进行相关的协议、流量分析、统计等来发现网络运行过程中出现的问题。

在应急响应与日常运维中都是至关重要的步骤。

对于取证分析，在[CTF中的流量包分析](https://wiki.x10sec.org/misc/traffic/introduction/)中有一些简单的介绍。二者基本上类似，不过在CTF中对数据包的分析比较注重细节和较高难度的处理；在美亚杯的比赛中更注重流量包的行为和结果，从而对其他镜像的分析结果进行佐证。

## Wireshark使用

pcap流量包的分析通常都是通过图形化的网络嗅探器——Wireshark进行的，这款嗅探器经过众多开发者的不断完善，现在已经成为了使用最为广泛的安全工具之一。

Wireshark的基本使用分为数据包筛选、数据包搜索、数据包还原、数据提取四个部分。

#### 数据包筛选

数据包筛选功能是Wireshark的核心功能，比如需要筛选出特定的协议如HTTP，Telnet等，也可能需要筛选出ip地址，端口等。

#### 数据包搜索

在Wireshark界面按“Ctrl+F”，可以进行关键字搜索。Wireshark的搜索功能支持正则表达式、字符串、十六进制等方式进行搜索，通常情况下直接使用字符串方式进行搜索。

#### 数据包还原

在Wireshark中，存在一个交追踪流的功能，可以将HTTP或TCP流量集合在一起并还原成原始数据，具体操作方式：选中想要还原的流量包，右键选中，选择追踪流 – TCP流/UPD流/SSL流/HTTP流。

#### 数据提取

Wireshark支持提取通过http传输（上传/下载）的文件内容，方法：选中http文件传输流量包，在分组详情中找到data或者Line-based text data:text/html层，鼠标右键点击 – 选中 导出分组字节流。

## 第五届美亚杯题解

#### 题目：

![avatar](https://k1ng0fic3.github.io/images/w1ki4.png)

![avatar](https://k1ng0fic3.github.io/images/w1ki2.png)

![avatar](https://k1ng0fic3.github.io/images/w1ki3.png)

#### 解析：

106、文件的MD5值：

这是美亚杯一个特点，疯狂求各种文件的MD5值。

Windows终端命令如下：

>certutil -hashfile filename MD5/SHA1/SHA256

![avatar](https://k1ng0fic3.github.io/images/w1ki1.png)

不过此题出了一些问题，跟答案对不上，最终作废了。

![avatar](https://k1ng0fic3.github.io/images/w1ki5.png)

107、Dos攻击：

从很多地方可以看出来攻击方式：

![avatar](https://k1ng0fic3.github.io/images/w1ki6.png)

[Slowhttptest](https://github.com/shekyan/slowhttptest)是依赖HTTP协议的慢速攻击DoS攻击工具，设计的基本原理是服务器在请求完全接收后才会进行处理，如果客户端的发送速度缓慢或者发送不完整，服务端为其保留连接资源池占用，大量此类请求并发将导致DoS。

108、抓包时间：

按时间排序后，查看最后一个包的Arrival Time。

![avatar](https://k1ng0fic3.github.io/images/w1ki7.png)

109、目标地址：

在107、中的图中可以看到Host信息，包括了发送的目的域名和端口号。

110、目标IP：

域名正确后，直接看建立连接的Destination。

![avatar](https://k1ng0fic3.github.io/images/w1ki8.png)

111、协议统计：

利用Wireshark的统计功能：统计 → 协议分级 得出HTTP占0.3%

![avatar](https://k1ng0fic3.github.io/images/w1ki9.png)

112、数据包个数：

比较简单，参照108、最下方包的序号。

根据下图解决最后两题：

![avatar](https://k1ng0fic3.github.io/images/w1ki10.png)

115、协议认定：

通过111、协议统计，HTTP的包只占了0.3%，不足以实现Slow HTTP Attack。故很明确被识别成了上层的TCP。

116、数据包信息：

根据Slow HTTP Attack模式中的Slow Header：

HTTP Header以两个CLRF结尾，通过发送只包含一个CLRF的畸形Header请求，占用Web服务器连接，消耗掉所有的连接数，而造成Web服务器拒绝新的服务。

也就是正常情况下的两个\r\n，现在只发了一个，来实现攻击。