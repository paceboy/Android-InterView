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
