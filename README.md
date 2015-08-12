# Android-InterView
android 面试汇总
##如果后台的Activity由于某原因被系统回收了，如何在被系统回收之前保存当前状态？
在onSaveInstanceState()中保存其状态。activity被置于后台时，在执行onStop之前，通过onSaveInstacneState
保存当前activity状态。如果被系统杀死，再次被调起时，，在onResume之前，onStart之后，通过
onRestoreInstanceState（）来恢复其状态。如果没有被系统杀死，则不执行onRestoreInstanceState。
如果用户点击back键，则onSaveInstanceState()不会被调用。onSaveInstanceState()在以下几种情况会被调用：
1，按home键时；2，横竖屏切换时；3，按电源键时；4，从当前activity进入另一个activity时。
系统默认只会保存ui状态--view hierarchy，比如editext里面的text，或者listview的scroll位置，
不会保存别的信息，如果需要保存则复写此方法。
##2，请解释下在单线程模型中Message、Handler、Message Queue、Looper之间的关系。
Message：包含消息id，存放携带消息数据的消息对象，MessageQueue
统一管理，handler发送和处理；
MessageQue 消息队列，管理message的队列，存放handler发过来的消息，链表串联，
经由looper抽取
Looper：消息泵，不断抽取messageQueue中的消息，一个messageque对应
一个looper。实现消息队列创建和消息循环功能。静态方法prepare和loop。
handler： 用来发送message和处理message。在使用handler之前必须使用looper创建了
队列，否则抛出异常。需要实现handleMessage来处理message。Thread 线程 负责调
度整个消息循环，即消息循环的执行场所。
有个问题，为什么非要出来个looper，而不是把looper的功能放到handler里面实现呢？
我觉得是面向对象的原则，由于looper里面的功能基本不怎么变化，也不需要用户了解，
因此将其封装，仅仅将handler和message对用户开放去调用。
![mahua]( https://github.com/paceboy/Android-InterView/blob/master/handler-howtowork.png)
##TCP的3次握手和4次挥手？
3次握手连接，首先连接端发送一个连接请求，syn为1，接收端回复一个确认表示同意连接，
连接端发送确认，表示收到接收端的连接确认，至此连接成功，可以全双工的发送数据。

4次断开连接，首先连接端发送fin为1的请求，表示自己不再发送数据，接收端收到后发送
确认，并再次发送一个自己的fin请求，表示自己也不再发送数据，连接端发送确认，至此
断开连接成功。
![mahua](https://github.com/paceboy/Android-InterView/blob/master/tcpconnect.png)
![mahua](https://github.com/paceboy/Android-InterView/blob/master/tcpdisconnect.png)
##tcp三次握手存在的风险？
攻击方伪造IP地址发送大量的SYN包，使服务端发送的SYN+ACK包得不到确认，服务器端将
为了维护一个非常大的半连Syn攻击就是 攻击客户端 在短时间内伪造大量不存在的IP地址，
向服务器不断地发送syn包，服务器回复确认包，并等待客户的确认，由于源地址是不存在的
，服务器需要不断的重发直 至超时，这些伪造的SYN包将长时间占用未连接队列，正常的SYN
请求被丢弃，目标系统运行缓慢，严重者引起网络堵塞甚至系统瘫痪。
Syn攻击是一个典型的DDOS攻击。检测SYN攻击非常的方便，当你在服务器上看到大量的半连
接状态时，特别是源IP地址是随机的，基本上可以断定这是一次SYN攻击.在Linux下可以如
下命令检测是否被Syn攻击
netstat -n -p TCP | grep SYN_RECV
一般较新的TCP/IP协议栈都对这一过程进行修正来防范Syn攻击，修改tcp协议实现。主要方
法有SynAttackProtect保护机制、SYN cookies技术、增加最大半连接和缩短超时时间等.
但是不能完全防范syn攻击。接列表而消耗非常多的资源，服务器失去响应，即服务器端受
到了SYN Flood攻击。
##为什么关闭连接要四次挥手？
TCP是全双工通道，一方发起关闭请求只关闭了一个方向上的通道，所以建立连接的双方都
要发起关闭请求并确认来关闭各自方向上的数据通道。
##为什么建立连接要三次握手？
目的： 防止已经失效的连接请求到达服务端，创建无效的连接，浪费资源。
说明： 当客户端发出的第一个连接请求在网络上的某个节点被滞留了（网络会存在许多不
可靠的因素），过一段时间后突然又到达了服务端，服务端误以为这是一个新的建立连接
的请求，于是就会向客户端发出确认包并建立连接。
实际上客户端当前并没有发出创建连接的请求，就会丢弃服务端的确认包。而服务端却创
建了连接并等待客户端发送数据，浪费了相关的资源。
### tcp拥塞控制机制？
计算机网络中的带宽、交换节点中缓存和处理机等，都是网络资源。在某段时间，若对网络
中某一资源的需求超过了该资源所能提供的可用部分，网络的性能就会变坏。这种情况就叫
做阻塞。拥塞控制就是防止过多的数据注入网络中，这样可以使网络中的路由器或链路不致
过载。拥塞控制是一个全局性的过程，和流量控制不同，流量控制指点对点通信量的控制。
(1) 拥塞窗口（cwnd）:拥塞控制的关键参数，它描述源端在拥塞控制情况下一次最多能发送
的数据包的数量。 
(2) 通告窗口（awin）:接收端给源端预设的发送窗口大小，它只在TCP连接建立的初始阶段
发挥作用。 
(3) 发送窗口（win）:源端每次实际发送数据的窗口大小。 
(4) 慢启动阈值（ssthresh）:拥塞控制中慢启动阶段和拥塞避免阶段的分界点。初始值通
常设为65535byte。 
(5) 回路响应时间（RTT）:一个TCP数据包从源端发送到接收端，源端收到接收端确认的时间
间隔。 
(6) 超时重传计数器（RTO）:描述数据包从发送到失效的时间间隔，是判断数据包丢失与否
及网络是否拥塞的重要参数。通常设为2RTT或5RTT。 
(7) 快速重传阈值(tcprexmtthresh):：能触发快速重传的源端收到重复确认包ACK的个数。
当此个数超过tcprexmtthresh时，网络就进入快速重传阶段。tcprexmtthresh缺省值为3。 
####1，慢开始与拥塞避免
发送方维持一个叫做拥塞窗口cwnd(congestion window)的状态变量。拥塞窗口的大小取决
于网络的拥塞程度，并且动态地在变化。发送方让自己的发送窗口等于拥塞窗口个，另外
考虑到接收方的接受能力，发送窗口可能小于拥塞窗口。
慢开始算法的思路就是，不要一开始就发送大量的数据，先探测一下网络的拥塞程度，
就是由小到大的逐渐增加拥塞窗口的大小。如图所示
![](https://github.com/paceboy/Android-InterView/blob/master/tcp%E6%85%A2%E5%BC%80%E5%A7%8B.png)

当建立连接后，设置cwnd为1（这里用报文段的个数的拥塞窗口大小举例说明慢开始算法
，实时拥塞窗口大小是以字节为单位的），发送1个数据包，接收方接收到后，发送确认
信息；cwnd变为1×2，发送2个数据包，接收方接收到后，发送2个确认信息；cwnd变为2×2
，发送4个数据包，接收方收到后，发送4个确认信息，cwnd变为4×2...
为了防止cwnd增长过大引起网络拥塞，还需设置一个慢开始门限ssthresh状态变量。
ssthresh的用法如下：
当cwnd<ssthresh时，使用慢开始算法。
当cwnd>ssthresh时，改用拥塞避免算法。
当cwnd=ssthresh时，慢开始与拥塞避免算法任意。
拥塞避免算法让拥塞窗口缓慢增长，即每经过一个往返时间RTT就把发送方的拥塞窗口
cwnd加1，而不是加倍。这样拥塞窗口按线性规律缓慢增长。
无论是在慢开始阶段还是在拥塞避免阶段，只要发送方判断网络出现拥塞（其根据就是
没有收到确认，虽然没有收到确认可能是其他原因的分组丢失，但是因为无法判定，所
以都当做拥塞来处理），就把慢开始门限设置为出现拥塞时的发送窗口大小的一半。然
后把拥塞窗口设置为1，执行慢开始算法。
####2，快重传和快恢复
快重传要求接收方在收到一个失序的报文段后就立即发出重复确认，而不要等到自己发
送数据是捎带确认。快重传算法规定，发送发只要一连收到3个重复确认就应当立即重传
对方尚未收到的报文段，而不必继续等待设置的重传计时器时间到期。
快重传配合使用的还有快恢复算法，有以下两个要点:
①当发送方连续收到三个重复确认时，就执行“乘法减小”算法，把ssthresh门限减半。但
是接下去并不执行慢开始算法。
②考虑到如果网络出现拥塞的话就不会收到好几个重复的确认，所以发送方现在认为网络
可能没有出现拥塞。所以此时不执行慢开始算法，而是将cwnd设置为ssthresh的大小，然
后执行拥塞避免算法。如下图：
![](https://github.com/paceboy/Android-InterView/blob/master/tcp%E5%BF%AB%E9%87%8D%E4%BC%A0.png)
参考：http://blog.csdn.net/sicofield/article/details/9708383
      http://blog.chinaunix.net/uid-27122224-id-3276910.html
      http://www.cnblogs.com/zhuzheic/archive/2013/04/08/3008364.html
      
###Android ipc的方式aidl的机制？与传统的linux ipc区别？
先说区别，aidl是解决android进程通信问题，是一种rpc的调用方式。由Dianne Hackbor提出。传统的linux通信有pipe管道、信号和跟踪。但其都局限于父进程与子进程之间，或者兄弟进程之间，后来增加了命名管道，使得不再局限于亲戚之间进程，后来AT&T Unix又增加报文队列，共享内存和信号量，BSD Linux增加了socket进程通信机制。为什么android自己设计一个新的ipc方式，原因如下：
另一方面是传输性能。socket作为一款通用接口，其传输效率低，开销大，主要用在跨网络的进程间通信和本机上进程间的低速通信。消息队列和管道采用存储-转发方式，即数据先从发送方缓存区拷贝到内核开辟的缓存区中，然后再从内核缓存区拷贝到接收方缓存区，至少有两次拷贝过程。共享内存虽然无需拷贝，但控制复杂，难以使用。
各种IPC方式数据拷贝次数如下表：
IPC	数据copy次数
共内存	0
Binder	1
Socket/Pipe/消息队列	2
	
还有一点是出于安全性考虑。Android作为一个开放式，拥有众多开发者的的平台，应用程序的来源广泛，确保智能终端的安全是非常重要的。终端用户不希望从网上下载的程序在不知情的情况下偷窥隐私数据，连接无线网络，长期操作底层设备导致电池很快耗尽等等。传统IPC没有任何安全措施，完全依赖上层协议来确保。首先传统IPC的接收方无法获得对方进程可靠的UID/PID（用户ID/进程ID），从而无法鉴别对方身份。Android为每个安装好的应用程序分配了自己的UID，故进程的UID是鉴别进程身份的重要标志。使用传统IPC只能由用户在数据包里填入UID/PID，但这样不可靠，容易被恶意程序利用。可靠的身份标记只有由IPC机制本身在内核中添加。其次传统IPC访问接入点是开放的，无法建立私有通道。比如命名管道的名称，system V的键值，socket的ip地址或文件名都是开放的，只要知道这些接入点的程序都可以和对端建立连接，不管怎样都无法阻止恶意程序通过猜测接收方地址获得连接。

Binder提供了远程过程调用RPC功能，英文意思是粘结剂的意思。在android的binder机制中，由一系列组件组成，分别是client，server，service manager和binder驱动程序，其中client，server和service manager运行在用户空间，binder驱动程序运行在内核空间，binder就是将这四个组件粘合在一起的粘结剂，其中核心组件便是binder驱动程序，service manger提供了辅助管理功能，client和server正式在binder驱动和service manger提供的基础设施上，进行client-server之间的通信。Service Manager和Binder驱动已经在Android平台中实现好，开发者只要按照规范实现自己的Client和Server组件就可以了。如图所示
 

调用方式的实现参考：http://blog.csdn.net/songjinshi/article/details/22918405

参考：http://www.cnblogs.com/albert1017/p/3849585.html

