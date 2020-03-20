# Miscellaneous

## 比特币交易

比特币交易的基本原理是：所有转账记录在比特币网络里，这个网络是基于区块链存储的。所有用户都可以查看这个去中心化的网络。

比特币钱包是怎么工作的：

//TODO

## Tor 网络

转载自 https://mp.weixin.qq.com/s/0vmNuEhsejMM0COSkfpnxw

【一篇文章读懂Tor原理】 crownless [看雪学院](https://mp.weixin.qq.com/s/0vmNuEhsejMM0COSkfpnxw) *2019-01-06*



我们都知道，在大多数情况下，当我们与一台远程服务器建立连接的时候，会将自己的IP地址泄漏给这台服务器。这似乎是不可避免的，否则，服务器怎么知道我们在哪儿，从而返回数据包给我们呢？泄漏IP地址会造成隐私泄漏，比如用户的地理位置信息会因为IP地址的泄漏而被服务器知道。



但是今天我们要介绍大名鼎鼎的Tor网络，用它可以做到一定程度上的匿名：服务器不能知道我们的真实IP地址。说到这里，或许你会产生一个疑问：这不就是代理吗？没错，Tor从某种角度来看也是一种代理。但Tor比一般的一重代理要高明得多，它是一种多重代理。用一重代理有一个坏处：如果一重代理本身是恶意的（想象一下你的VPN提供商是恶意的），那么它就可以知道你访问过什么网站。显然，这不能满足我们的“谁都不能知道是我访问了网站”的隐私需求。



事实上，Tor是一种“代理节点快速动态变化的加密三重代理”。如下图所示，Alice为了匿名访问Bob服务器，将会随机选择Tor网络中的三个节点（分别称为节点A、节点B、节点C），将自己的网络流量通过这条随机路径送达Bob服务器。



![img](https://raw.githubusercontent.com/XDforensics-wiki/XDforensics-wiki/master/res/others/howtorworks.jpg)



这么做有如下好处：Bob只能知道数据包来自节点C（在术语中称为出口节点，exit node），节点C只能知道有数据从节点B发送到了Bob，节点B只能知道有数据从节点A发送到了节点C，节点A只能知道Alice访问了节点B。而且，这条路径会定时拆除并重新选择节点建立新路径。这使得攻击者（如政府）难以通过拿下单个或少数节点来获取用户的完整访问记录。



那么Tor是如何确保数据在不同节点之间传送时是加密的呢？如果不加密，那么恶意节点就可以知道它传送的内容是什么，从而就有可能知道是Alice访问了Bob。为了避免这种情况的发生，Tor采用了多重加密。事实上，这也正是Tor（The Onion Network）为什么叫洋葱路由的原因，因为真实数据被一层层加密，就好像洋葱一层一层的肉一样（见下图）。



Bob为了访问Alice，先访问Tor的目录服务器，获取一部分Tor节点的IP地址，并从中随机选择三个节点的IP地址A、B、C。然后，Bob和节点A通过Curve25519椭圆曲线（以前用Diffie-Hellman密钥交换算法）协商一个对称密钥keyA。这是一种很神奇的可以在不安全的信道上建立共享的对称密钥的方法。之后，Bob和节点A之间就会用这把对称密钥keyA进行加密通信。



然后，Bob把B的IP地址和与B协商密钥所需的参数用对称密钥keyA加密后发送给A。A用keyA解密后，将Bob与B协商密钥所需的参数发送到B的IP地址。B收到参数后产生了对称密钥keyB，并将与Bob协商密钥所需的参数发还给A。A将参数通过keyA加密后发还给Bob。Bob通过算法计算出对称密钥keyB。Bob通过相同的方法和C协商出keyC。至此，Bob有了三把钥匙keyA、keyB、keyC。



![img](https://raw.githubusercontent.com/XDforensics-wiki/XDforensics-wiki/master/res/others/tor2.jpg)



Bob往Alice发送数据包时，先将数据Data用keyC加密，再用keyB加密，再用keyA加密，就好像层层包裹一样，然后发往节点A。节点A解开一层加密，发往节点B。节点B解开一层加密，发往节点C。节点C解开一层加密，得到Bob发往Alice的明文，发送给Alice。



你可能会问，如果Bob和Alice之间采用HTTP明文通信，那节点C岂不是能看到通信明文了吗？的确是这样，而且无法避免C是恶意节点。所以最好采用HTTPS通信。



\- End -

------

补充：

普通使用的3台代理对用户是可见的，我们知道自己的数据经过了哪三台服务器到达了自己想要的网站。

Tor 为这种匿名代理新增了一种功能：onion 网络

![](https://raw.githubusercontent.com/XDforensics-wiki/XDforensics-wiki/master/res/others/onion.png)

访问onion域名时，tor 依然会首先经过三次代理，随后最后一次代理连接一台匿名的服务器1，这台匿名服务器再次连接一台匿名服务器2，匿名服务器2再次连接匿名服务器3，匿名服务器3最终连接onion网站。存放onion网站的服务器地址不为人知，被完全隐藏起来。

大众俗称的“暗网”，即为onion网络。

duckduckgo ： https://3g2upl4pq6kufc4m.onion