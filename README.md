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
